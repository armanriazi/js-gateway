
```javascript



import ControllerBase from "../../services/controllerBase.js";
import rabbit from "../../services/broker.js";
import responseMessage from "../../common/responseMessage.js";
import path from "path";
import utils from "../../services/utils.js";
import logger from "../../services/backendLogger.js";
import { createReadStream, existsSync } from "fs";
import {
  // buildRequestOptions,
  generateRedisKey,
  handleFileRequest,
  handleNonFileRequest,
  handleSRTProxyRequest,
  setRedisKey,
  updateCounts,
} from "./aiServices.service.js";
import messages from "../../common/messages.js";
// import request from 'request'

/**
 * representive class of users controllers
 * @class
 */
export default new (class Controller extends ControllerBase {
  /**
   * @function proxyOrDirect
   * @description proxy or direct
   * @param {object} req - request object
   * @param {object} res - response object
   */
  proxyOrDirect = async (req, res) => {
    if (req.requestType === "proxy") {
      return this.proxyToServices(req, res);
    } else {
      // if req.requestType is equal to direct or null and method is post
      return this.create(req, res);
    }
  };

  /**
   * @function create
   * @description creates a new user
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  create = async (req, res, next) => {
    try {
      const { serviceName, accessAiService } = req;

      let controller;

      switch (serviceName) {
        /*  case 'faceBlurring':
        case 'faceMorphing':
          controller = this.faceBlurringMorphingController
          break
*/
        case "faceVerification":
          controller = this.faceVerificationController;
          break;
        default:
          controller = this.baseController;
      }

      return await controller({ accessAiService, res, req });
    } catch (error) {
      next(error);
    }
  };

  /**
   * @function get
   * @description get file
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  get = async (req, res) => {
    try {
      const { originalUrl, accessAiService } = req;
      const baseName = path.basename(originalUrl);

      if (req.requestType === "proxy") {
        return this.proxyToServices(req, res);
      } else if (baseName === "checkServerHealth") {
        return await this.checkServerHealth(accessAiService, req, res);
      } else {
        return this.serveFile(originalUrl, res);
      }
    } catch (error) {
      return responseMessage({
        res,
        status: "error",
        data: error,
      });
    }
  };

  /**
   * تشخیص سلامت سرور
   * @function checkServerHealth
   * @description تشخیص سلامت سرور
   * @param {object} req - request
   * @param {object} res - response message
   * @param {string} res.message - status of server is ok or error
   * @memberof liveness
   */
  checkServerHealth = async (accessAiService, req, res) => {
    try {
      const msg = {
        data: "checkHealth",
      };
      const { queue } = req.accessAiService.Service;
      const { isAllowedIP } = req;
      const apiId = accessAiService.id;
      const value = isAllowedIP || accessAiService.ApiKey.value;
      const rabbitMQ = await rabbit.getInstance();
      const { result } = JSON.parse(
        await rabbitMQ.sendRpcMessage({ msg, queue })
      );
      const { messageCount } = await rabbitMQ.messageCount(queue);

      const statusCode = result === "ok" ? 200 : 500;
      const status = result === "ok" ? "success" : "error";

      const redisKey = generateRedisKey(apiId);
      setRedisKey(redisKey, new Date());

      if (result) {
        await updateCounts(value, apiId);
      }

      return responseMessage({
        res,
        status,
        data: { statusCode, numberOfMessagesInQueue: messageCount },
      });
    } catch (error) {
      console.log(error);
    }
  };

  /**
   * createMessage
   * @function createMessage
   * @description createMessage
   * @param {object} accessAiService - Ai Service For ApiKey
   * @param {object} req - request object
   * @param {object} res - response message
   * @return {object}
   */
  createMessage = ({ req, files }) => {
    // const { imagePath, checksum, base64, ...msg } = { ...req.body }
    let { ...msg } = { ...req.body };
    const { filePath } = req;

    for (const file of files) {
      if (file.howGet === "base64" || file.howGet === "link") {
        delete msg[file.howGet];
      }
    }
    msg = { ...msg, ...filePath };

    return msg;
  };

  /**
   * Serve a file or return an error response based on the provided original URL.
   *
   * @param {string} originalUrl - The original URL to process.
   * @param {object} res - The Express.js response object to send the file or error response.
   * @returns {Promise<void>} - A Promise that resolves once the file is served or the error response is sent.
   */
  serveFile = (originalUrl, res) => {
    try {
      const relativePath = originalUrl.split("/").slice(3).join("/");
      const filePath = path.join(
        `${process.cwd()}`,
        "../archive/",
        relativePath
      );

      if (existsSync(filePath)) {
        res.sendFile(filePath);
      } else {
        return responseMessage({
          res,
          status: "error",
          data: messages.m.NOT_FOUND_ARCHIVE,
        });
      }
    } catch (error) {
      console.error(error);
      return responseMessage({
        res,
        status: "error",
        data: messages.m.FILE_SERVING_ERROR,
      });
    }
  };

  /**
   * Base controller to handle API requests.
   *
   * @param {object} accessAiService - Ai Service For ApiKey
   * @param {object} req - request object
   * @param {object} res - response object
   * @return {object}
   */
  baseController = async ({ accessAiService, req, res }) => {
    if (req.projectName === "nlp") {
      return this.defaultNLPController({ accessAiService, req, res });
    } else {
      return this.defaultController({ accessAiService, req, res });
    }
  };

  /**
   * @function defaultController
   * @description default Controller
   * @param {object} accessAiService - Ai Service For ApiKey
   * @param {object} req - request object
   * @param {object} res - response object
   * @return {object}
   */
  defaultController = async ({ accessAiService, res, req }) => {
    try {
      utils.filterValues(req);

      const { queue } = accessAiService.Service;
      const msg = this.createMessage({ req, files: accessAiService.files });
      const apiId = accessAiService.id;
      const { gatewayLogData, isAllowedIP, originalUrl } = req;
      const value = isAllowedIP || accessAiService.ApiKey.value;

      const beforeSendTime = new Date();
      const rabbitMQ = await rabbit.getInstance();

      let { result } = JSON.parse(
        await rabbitMQ.sendRpcMessage({ msg, queue })
      );

      const aiTimeProcessing = Date.now() - beforeSendTime;

      if (result) {
        result = utils.addNestedImagePath(result, originalUrl);
      }

      logger.responseLog({ res, data: result });

      gatewayLogData.push({ aiTimeProcessing });
      const redisKey = generateRedisKey(apiId);
      setRedisKey(redisKey, new Date());

      if (result) {
        await updateCounts(value, apiId);
      }

      return responseMessage({
        res,
        statusCode: 200,
        data: result,
      });
    } catch (err) {
      logger.errorLog({ res, err });
    }
  };

  /**
   * @function faceVerificationController
   * @description faceVerification Controller
   * @param {object} accessAiService - Ai Service For ApiKey
   * @param {object} req - request object
   * @param {object} res - response object
   * @return {object} image path
   */
  faceVerificationController = async ({ accessAiService, res, req }) => {
    const { queue } = accessAiService.Service;
    const message = this.createMessage({ req, files: accessAiService.files });
    const apiId = accessAiService.id;
    const { gatewayLogData, isAllowedIP, originalUrl } = req;
    const value = isAllowedIP || accessAiService.ApiKey.value;

    let { type, userId, occlusion } = req.body;

    if (!type) {
      type = "personalPicture";
    }

    const msg = {
      selfie: message.selfie,
      personalPicture: message.personalPicture,
      type,
      userId,
      nationalCode: 0,
      occlusion: occlusion || false,
    };

    const beforeSendTime = new Date();
    const rabbitMQ = await rabbit.getInstance();
    let { result } = JSON.parse(await rabbitMQ.sendRpcMessage({ msg, queue }));

    const aiTimeProcessing = Date.now() - beforeSendTime;

    if (result !== "error" || Object.keys(result) > 0) {
      result = utils.addNestedImagePath(result, originalUrl);
    }

    gatewayLogData.push({ aiTimeProcessing });
    const redisKey = generateRedisKey(apiId);
    setRedisKey(redisKey, new Date());

    if (result) {
      await updateCounts(value, apiId);
    }

    return responseMessage({
      res,
      statusCode: 200,
      data: result,
    });
  };

  /**
   * @function defaultNLPController
   * @description default NLP Controller
   * @param {object} accessAiService - Ai Service For ApiKey
   * @param {object} req - request object
   * @param {object} res - response object
   * @return {object}
   */
  defaultNLPController = async ({ accessAiService, res, req }) => {
    try {
      const msg = req.body;
      const { queue } = accessAiService.Service;
      const apiId = accessAiService.id;
      const { gatewayLogData, isAllowedIP } = req;
      const value = isAllowedIP || accessAiService.ApiKey.value;

      const beforeSendTime = new Date();
      const rabbitMQ = await rabbit.getInstance();
      const { result } = JSON.parse(
        await rabbitMQ.sendRpcMessage({ msg, queue })
      );

      logger.responseLog({ res, data: result });

      const aiTimeProcessing = Date.now() - beforeSendTime;

      gatewayLogData.push({ aiTimeProcessing });

      const redisKey = generateRedisKey(apiId);
      setRedisKey(redisKey, new Date());

      if (result) {
        await updateCounts(value, apiId);
      }

      return responseMessage({
        res,
        statusCode: 200,
        data: result,
      });
    } catch (err) {
      logger.errorLog({ res, err });
    }
  };

  /**
   * @function faceBlurringMorphingController
   * @description faceBlurring and faceMorphing Controller
   * @param {object} accessAiService - Ai Service For ApiKey
   * @param {object} req - request object
   * @param {object} res - response object
   * @return {object} image path
   */
  faceBlurringMorphingController = async ({ accessAiService, res, req }) => {
    try {
      const { blurLevel, ...inputObject } = req.body;
      let imagePath = null;

      utils.filterValues(req);

      const { queue } = accessAiService.Service;
      const msg = this.createMessage({ req, files: accessAiService.files });
      const apiId = accessAiService.id;
      const { gatewayLogData, isAllowedIP, originalUrl } = req;
      const value = isAllowedIP || accessAiService.ApiKey.value;

      const beforeSendTime = new Date();
      const rabbitMQ = await rabbit.getInstance();

      const streamData = createReadStream(msg.data, {
        encoding: "base64",
      });
      let str = "";
      streamData
        .on("data", (chunk) => {
          str += chunk;
        })
        .on("end", async () => {
          // image path
          if (inputObject.base64) {
            imagePath = true;
          } else {
            imagePath = inputObject.imagePath;
          }
          const msg = {
            data: str,
            imagePath,
            location: inputObject.location,
            blurLevel,
          };

          let { result } = JSON.parse(
            await rabbitMQ.sendRpcMessage({ msg, queue })
          );

          const aiTimeProcessing = Date.now() - beforeSendTime;

          if (result) {
            result = utils.addNestedImagePath(result, originalUrl);
          }

          logger.responseLog({ res, data: result });

          gatewayLogData.push({ aiTimeProcessing });

          const redisKey = generateRedisKey(apiId);
          setRedisKey(redisKey, new Date());

          if (result) {
            await updateCounts(value, apiId);
          }

          return responseMessage({
            res,
            statusCode: 200,
            data: result,
          });
        })
        .on("err", () => {
          return responseMessage({
            res,
            statusCode: 500,
            status: "error",
          });
        });
    } catch (err) {
      logger.errorLog({ res, err });
    }
  };

  /**
   * @function proxyToServices
   * @description proxy To Services
   * @param {object} req - request object
   * @param {object} res - response object
   * @return {object} image path
   */
  proxyToServices = async (req, res) => {
    try {
      const { gatewayLogData, isAllowedIP, accessAiService } = req;
      const apiId = accessAiService.id;
      const value = isAllowedIP || accessAiService.ApiKey.value;

      console.log(req.hasFile);
      if (req.srtProxy) {
        await handleSRTProxyRequest(req, res, apiId, value, gatewayLogData);
      } else if (req.hasFile || req.method === "GET") {
        await handleFileRequest(req, res, apiId, value, gatewayLogData);
      } else {
        await handleNonFileRequest(req, res, apiId, value, gatewayLogData);
      }
    } catch (error) {
      console.error(error);
    }
  };
})();
```