
```javascript



import crypto, { randomUUID } from "crypto";
import fs from "fs";
import path from "path";
import WebSocket from "ws";
import messages from "../common/messages.js";
import apisService from "../modules/apis/apis.service.js";
import apiKeysService from "../modules/apiKeys/apiKeys.service.js";
import redis from "../config/redis.js";
import Rabbit from "../services/broker.js";
// import logger from '../services/backendLogger'
import utils from "../services/utils.js";
import sequelize from "sequelize";
import gatewayLogsService from "../modules/gatewayLogs/gatewayLogs.service.js";
// import server from '../app'

// Create a WebSocket server instance
const wss = new WebSocket.Server({ port: 8080 });

export const webSocketHandler = () => {
  // Event: WebSocket server is listening and ready
  wss.on("listening", () => {
    // console.log('WebSocket server is listening on port 8080')
  });

  // Event: New WebSocket connection established
  wss.on("connection", async (ws, req) => {
    try {
      console.log("New WebSocket connection established");

      const { headers, url } = req;
      const gatewayService = url.split(path.sep)[1];
      const request = {
        originalUrl: url,
        headers,
        ip: req.headers["x-forwarded-for"] || req.connection.remoteAddress,
        logType: "gateway",
      };

      let hasError = null;
      if (!url.includes("checkServerHealth")) {
        let uniqueKey = null;

        if (gatewayService === "service") {
          const apiKey = req.headers.apikey;
          uniqueKey = apiKey;
          request.logType = "websocket";
        }

        const gatewayLog = await gatewayLogsService.create({
          uniqueKey,
          data: [{ request }],
          status: "error",
        });

        if (gatewayLog.status === "error") {
          hasError = true;
        } else {
          gatewayLog && (req.gatewayLogId = gatewayLog.id);
          gatewayLog && (req.gatewayLogData = gatewayLog.data);
        }
      }

      let receivedData = Buffer.alloc(0);

      ws.on("message", async (message) => {
        try {
          if (hasError) {
            const errorMessage = {
              status: 400,
              message: messages.m.CLIENT_ERROR,
            };
            ws.send(JSON.stringify(errorMessage));
            return;
          }

          const checkResult = await checkApi(req);

          if (checkResult.status > 200) {
            await updateLog({ result: checkResult, status: "error", req });
            ws.send(JSON.stringify(checkResult));

            return;
          }

          // serviceName = checkResult.req.serviceName,
          const queue = checkResult.req.accessAiService.Service.queue;
          const apiId = checkResult.req.accessAiService.id;
          const value = checkResult.req.accessAiService.ApiKey.value;

          // Parse the received message as JSON
          const dataObject = JSON.parse(message);

          // Access the file data and additional information
          let fileData = dataObject.fileData.data;
          const flags = dataObject.flags;

          fileData = Buffer.from(fileData);

          if (fileData instanceof Buffer) {
            // Accumulate the received data into a buffer
            receivedData = Buffer.concat([receivedData, Buffer.from(fileData)]);
          }
          const imageExtension = getImageExtension(receivedData);

          if (imageExtension) {
            const filename = `gateway-socket-${randomUUID()}${imageExtension}`;
            const filePath = `${process.cwd()}/assets/uploads/${filename}`;

            // Save the image data to a file
            fs.writeFile(filePath, receivedData, async (err) => {
              if (err) {
                console.error("Error saving image:", err);
              } else {
                console.log(`Image saved as ${filename}`);

                const msg = createMessage({
                  req: flags,
                  files: checkResult.req.accessAiService.files,
                  filePath,
                });

                request.body = msg;

                const beforSendTime = new Date();
                const rabbitMQ = await Rabbit.getInstance();
                const { result } = JSON.parse(
                  await rabbitMQ.sendRpcMessage({ msg, queue })
                );

                const aiTimeProcessing = Date.now() - beforSendTime;

                await updateLog({
                  request,
                  msg,
                  aiTimeProcessing,
                  result,
                  status: "success",
                  req,
                });

                const redisKey = `${apiId}::${crypto.randomUUID()}`;
                utils.redisSetKey({
                  key: redisKey,
                  seconds: 60,
                  value: new Date(),
                });

                if (result) {
                  await apiKeysService.updateOne(
                    { maxCount: sequelize.literal("max_count - 1") },
                    { value }
                  );

                  await apisService.updateOne(
                    { maxCount: sequelize.literal("max_count - 1") },
                    { id: apiId }
                  );
                }

                const resultString = JSON.stringify(result);

                ws.send(resultString);
              }
            });
          }
        } catch (error) {
          console.error("Error parsing received message:", error);
        }
      });

      // Event: WebSocket connection closed
      ws.on("close", () => {
        console.log("WebSocket connection closed");
      });
    } catch (error) {
      console.log(error);
    }
  });
};

const getImageExtension = (data) => {
  const uint = new Uint8Array(data);
  let extension = "";

  console.log(uint.length, uint[0], uint[1], "here");
  if (uint.length >= 2) {
    if (uint[0] === 0xff && uint[1] === 0xd8) {
      extension = ".jpg";
    } else if (uint[0] === 0x89 && uint[1] === 0x50) {
      extension = ".png";
    } else if (uint[0] === 0x42 && uint[1] === 0x4d) {
      extension = ".bmp";
    } else if (uint[0] === 0x49 && uint[1] === 0x44 && uint[2] === 0x33) {
      extension = ".mp3";
    } else if (uint[0] === 0x00 && uint[1] === 0x00) {
      extension = ".mp4";
    } else if (
      uint[0] === 0x52 &&
      uint[1] === 0x49 &&
      uint[2] === 0x46 &&
      uint[3] === 0x46
    ) {
      extension = ".wav";
    } else if (
      uint[0] === 0x4f &&
      uint[1] === 0x67 &&
      uint[2] === 0x67 &&
      uint[3] === 0x53
    ) {
      extension = ".ogg";
    } else if (
      uint[0] === 0x66 &&
      uint[1] === 0x4c &&
      uint[2] === 0x61 &&
      uint[3] === 0x43
    ) {
      extension = ".flac";
    } else if (
      uint[0] === 0x6d &&
      uint[1] === 0x6f &&
      uint[2] === 0x6f &&
      uint[3] === 0x76
    ) {
      extension = ".mov";
    } else if (
      uint[0] === 0x41 &&
      uint[1] === 0x56 &&
      uint[2] === 0x49 &&
      uint[3] === 0x20
    ) {
      extension = ".avi";
    } else if (
      uint[0] === 0x30 &&
      uint[1] === 0x26 &&
      uint[2] === 0xb2 &&
      uint[3] === 0x75
    ) {
      extension = ".wmv";
    } else if (
      uint[0] === 0x1a &&
      uint[1] === 0x45 &&
      uint[2] === 0xdf &&
      uint[3] === 0xa3
    ) {
      extension = ".webm";
      // eslint-disable-next-line no-dupe-else-if
    } else if (
      // eslint-disable-next-line no-dupe-else-if
      uint[2] === 0x01 &&
      uint[3] === 0xba &&
      uint[0] === 0x00 &&
      uint[1] === 0x00
    ) {
      extension = ".mpeg";
    }
  }

  console.log(extension);
  return extension;
};

const checkApi = async (req) => {
  try {
    const { url } = req;
    const apiKey = req.headers.apikey;
    const serviceName = url.split(path.sep)[2];
    const baseName = path.basename(url);

    if (!apiKey) return { status: 400, message: messages.m.API_KEY_REQUIRED };

    if (!serviceName)
      return { status: 400, message: messages.m.SERVICE_NOT_FOUND };

    const accessAiService = await apisService.findAiServiceForApiKey({
      apiKey,
      serviceName,
    });

    if (!accessAiService) {
      return { status: 403, message: messages.m.INVALID_APIKEY_OR_SERVICE };
    }

    if (accessAiService.ApiKey.maxCount < 1) {
      await apiKeysService.updateOne(
        { isValid: false },
        { id: accessAiService.ApiKey.id }
      );
      return { status: 400, message: messages.m.COUNT_API_KEY_ZERO };
    }

    if (accessAiService.maxCount === 0) {
      return { status: 400, message: messages.m.COUNT_API_MAX_COUNT_ZERO };
    }

    const dateNow = new Date();

    if (accessAiService.ApiKey.expireDate < dateNow) {
      await apiKeysService.updateOne(
        { isValid: false },
        { id: accessAiService.ApiKey.id }
      );
      return { status: 400, message: messages.m.API_KEY_EXPIRE };
    }

    if (!accessAiService.ApiKey.isValid) {
      return { status: 400, message: messages.m.API_KEY_INVALID };
    }

    const resultServiceApi = checkValidApis({
      apis: accessAiService.Service.apis,
      url,
      baseName,
    });
    const validApiService = resultServiceApi.isValidApi;

    accessAiService.files = resultServiceApi.files;
    if (!validApiService) {
      return { status: 400, message: messages.m.NOT_FOUND_ROUTE_ERROR };
    }

    const resultApiKeyApi = checkValidApis({
      apis: accessAiService.apis,
      url,
      baseName,
    });
    const validApiApiKey = resultApiKeyApi.isValidApi;

    if (!validApiApiKey)
      return { status: 400, message: messages.m.NOT_ALLOW_API };

    const { countTpm } = await getCountTmp(accessAiService);

    if (countTpm >= accessAiService.TPM) {
      return { status: 400, message: messages.m.MAX_COUNT_TPM_INVALID };
    }

    req.accessAiService = accessAiService;
    req.serviceName = serviceName;
    req.files = [];
    req.projectName = accessAiService.Service.Project.name;

    return { status: 200, req };
  } catch (err) {
    return err;
  }
};

const getCountTmp = (accessAiService) => {
  return new Promise((resolve, reject) => {
    try {
      const redisKeyPattern = `${accessAiService.id}::*`;
      const stream = redis.scanStream({
        match: redisKeyPattern,
        count: 10,
      });
      let countTpm = 0;
      stream.on("data", async (keys = []) => {
        countTpm += keys.length;
      });
      stream.on("end", () => {
        resolve({ countTpm });
      });
    } catch (error) {
      reject(error);
    }
  });
};

const checkValidApis = ({ apis, url, baseName }) => {
  let isValidApi = false;
  let files = [];
  for (const api of apis) {
    if (url.includes(api.path) && path.basename(api.path) === baseName) {
      isValidApi = true;
      api.files && (files = api.files);
      break;
    }
  }

  return { isValidApi, files };
};

const createMessage = ({ req, files, filePath }) => {
  const { ...msg } = { ...req };

  for (const file of files) {
    if (file.howGet === "base64" || file.howGet === "link") {
      delete msg[file.howGet];
    }

    msg[file.name] = filePath;
  }

  return msg;
};

const updateLog = async ({
  request = null,
  msg = null,
  aiTimeProcessing = null,
  result,
  status,
  req,
}) => {
  const { gatewayLogData, gatewayLogId, url } = req;
  if (!url.includes("checkServerHealth")) {
    if (status === "success") {
      request.body = msg;

      gatewayLogData[0].request = request;

      gatewayLogData.push({ aiTimeProcessing });

      gatewayLogData.push({ response: result });
      await gatewayLogsService.updateOne(
        {
          data: gatewayLogData,
          status,
        },
        { id: gatewayLogId }
      );
    } else {
      gatewayLogData.push({ response: result });

      await gatewayLogsService.updateOne(
        {
          data: gatewayLogData,
          status,
        },
        { id: gatewayLogId }
      );
    }
  }
};
```