
```javascript



import {
  Server,
  loadPackageDefinition,
  ServerCredentials,
} from "@grpc/grpc-js";

import redis from "../config/redis.js";
import * as protoLoader from "@grpc/proto-loader";

import messages from "../common/messages.js";
import Rabbit from "../services/broker.js";
import apiKeysService from "../modules/apiKeys/apiKeys.service.js";
import apisService from "../modules/apis/apis.service.js";

const grpcServer = new Server({
  "grpc.max_receive_message_length": 10 * 1024 * 1024, // 10 MB
});

const PROTO_PATH = `${process.cwd()}/controller.proto`;

const packageDefinition = protoLoader.loadSync(PROTO_PATH);

const { Controller } = loadPackageDefinition(packageDefinition);

// Create the gRPC handler module
export const grpcHandler = () => {
  // Add your gRPC service implementation to the server
  grpcServer.addService(Controller.RemoteService.service, {
    /**
     * Implements RPC methods.
     */

    checkServerHealth: async (call, callback) => {
      try {
        const checkResponse = await checkApi(call);

        if (checkResponse.status === "error") {
          callback(null, checkResponse);
          return;
        }

        console.log("Received data from client:", call.request);
        const msg = {
          data: "checkHealth",
        };
        const queue = checkResponse.accessAiService.Service.queue;
        const rabbitMQ = await Rabbit.getInstance();
        const { result } = JSON.parse(
          await rabbitMQ.sendRpcMessage({ msg, queue })
        );
        const { messageCount } = await rabbitMQ.messageCount(queue);

        const statusCode = result === "ok" ? 200 : 500;
        const status = result === "ok" ? "success" : "error";

        const responseMessage = {
          statusCode,
          numberOfMessagesInQueue: messageCount,
        };

        // Send a response back to the client
        callback(null, { status, message: responseMessage });
      } catch (error) {
        console.log(error);
      }
    },

    // Implement the SendFile method
    sendFileSpeech: async (call, callback) => {
      const checkResponse = await checkApi(call);

      if (checkResponse.status === "error") {
        callback(null, checkResponse);
        return;
      }

      const queue = checkResponse.accessAiService.Service.queue;

      const msg = call.request;

      const fileResponse = {};

      const rabbitMQ = await Rabbit.getInstance();
      const { result } = JSON.parse(
        await rabbitMQ.sendRpcMessage({ msg, queue })
      );

      if (result) {
        fileResponse.status = "success";
        fileResponse.result = result;
      } else {
        fileResponse.status = "error";
        fileResponse.error = "no data";
      }

      callback(null, fileResponse);
    },
  });

  async function checkApi(call) {
    const metadata = call.metadata.internalRepr;
    const apiKey = metadata.get("apikey")[0];
    const serviceName = metadata.get("service")[0];

    // console.log(serviceName)

    if (!apiKey) throw global.error(400, messages.m.API_KEY_REQUIRED);

    if (!serviceName) throw global.error(400, messages.m.SERVICE_NOT_FOUND);

    const accessAiService = await apisService.findAiServiceForApiKey({
      apiKey,
      serviceName,
    });

    // console.log(apiKey, serviceName, accessAiService)

    if (!accessAiService) {
      const response = {
        status: "error",
        error: messages.m.INVALID_APIKEY_OR_SERVICE,
      };

      return response;
    }

    if (accessAiService.ApiKey.maxCount < 1) {
      await apiKeysService.updateOne(
        { isValid: false },
        { id: accessAiService.ApiKey.id }
      );

      const response = {
        status: "error",
        error: messages.m.COUNT_API_KEY_ZERO,
      };

      return response;
    }

    if (accessAiService.maxCount === 0) {
      const response = {
        status: "error",
        error: messages.m.COUNT_API_MAX_COUNT_ZERO,
      };

      return response;
    }

    const dateNow = new Date();

    if (accessAiService.ApiKey.expireDate < dateNow) {
      await apiKeysService.updateOne(
        { isValid: false },
        { id: accessAiService.ApiKey.id }
      );

      const response = {
        status: "error",
        error: messages.m.API_KEY_EXPIRE,
      };

      return response;
    }

    if (!accessAiService.ApiKey.isValid) {
      const response = {
        status: "error",
        error: messages.m.API_KEY_INVALID,
      };

      return response;
    }

    const { countTpm } = await getCountTmp(accessAiService);

    console.log(countTpm, accessAiService.TPM);
    if (countTpm >= accessAiService.TPM) {
      const response = {
        status: "error",
        error: messages.m.MAX_COUNT_TPM_INVALID,
      };

      return response;
    }

    const req = {};

    req.accessAiService = accessAiService;
    req.serviceName = serviceName;
    req.files = [];
    req.projectName = accessAiService.Service.Project.name;

    return req;
  }

  function getCountTmp(accessAiService) {
    return new Promise((resolve, reject) => {
      try {
        const redisKeyPattern = `${accessAiService.id}::*`;
        const stream = redis.scanStream({
          match: redisKeyPattern,
          count: 10,
        });

        // console.log(redisKeyPattern)
        let countTpm = 0;
        stream.on("data", async (keys = []) => {
          // console.log('hereeee', keys)
          countTpm += keys.length;
        });
        stream.on("end", () => {
          resolve({ countTpm });
        });
      } catch (error) {
        reject(error);
      }
    });
  }

  // Start the gRPC server
  grpcServer.bindAsync(
    "0.0.0.0:3020",
    ServerCredentials.createInsecure(),
    (error) => {
      if (error) {
        console.error("Failed to bind gRPC server:", error);
        return;
      }
      // console.log('gRPC server is listening on port:', port)
      grpcServer.start();
    }
  );

  // Return the gRPC server instance
  return grpcServer;
};
```