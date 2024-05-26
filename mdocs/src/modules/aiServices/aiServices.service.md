
```javascript



import path, { extname } from "path";
import apisService from "../apis/apis.service.js";
import apiKeysService from "../apiKeys/apiKeys.service.js";
import messages from "../../common/messages.js";
import { randomUUID } from "crypto";
import { createWriteStream, statSync, promises, createReadStream } from "fs";
import axios from "../../common/helper/axios.js";
import { Readable } from "stream";
import redis from "../../config/redis.js";
import FormData from "form-data";
import servicesService from "../services/services.service.js";
import sequelize from "sequelize";
import utils from "../../services/utils.js";
import https from "https";
import request from "request";
import responseMessage from "../../common/responseMessage.js";

/**
 * check api is valid if is not valid return not found or access denied
 * @type {function}
 * @param {object} req - express request object
 * @param {object} res - express response object
 * @param {function} next - function that sends request to the next middleware
 */
export const checkApi = async (req, res, next) => {
  try {
    const { isAllowedIP, method, originalUrl } = req;
    const apiKey = req.get("ApiKey");
    const { originalURL, baseName, serviceName } = adjustServiceAndBaseName(
      originalUrl,
      method
    );

    if (!isAllowedIP && !apiKey)
      throw global.error(400, messages.m.API_KEY_REQUIRED);

    if (!serviceName) throw global.error(400, messages.m.SERVICE_NOT_FOUND);

    let accessAiService = null;

    if (!isAllowedIP) {
      accessAiService = await getAccessAiService(apiKey, serviceName);

      validateAccessAiService(accessAiService);

      validateApiMethods(accessAiService, method, originalURL, baseName);
    } else {
      accessAiService = await getServiceInfo(serviceName, method);
    }

    await validateMaxCountTpm(accessAiService);

    req.requestType = accessAiService.Service.type;
    req.accessAiService = accessAiService;
    req.serviceName = serviceName;

    if (method === "POST") {
      req.projectName = accessAiService.Service.Project.name;
      req.files = [];
    }

    next();
  } catch (err) {
    next(err);
  }
};

/**
 * check api is valid if is not valid return not found or access denied
 * @type {function}
 * @param {object} req - express request object
 * @param {object} res - express response object
 * @param {function} next - function that sends request to the next middleware
 */
export const checkProxyApi = async (req, res, next) => {
  try {
    const { isAllowedIP, method, originalUrl } = req;
    const apiKey = req.get("ApiKey");
    const { originalURL, baseName, serviceName, absolutePath } =
      adjustServiceAndBaseName(originalUrl, method);

    if (!isAllowedIP && !apiKey)
      throw global.error(400, messages.m.API_KEY_REQUIRED);

    if (!serviceName) throw global.error(400, messages.m.SERVICE_NOT_FOUND);

    let accessAiService = null;

    if (!isAllowedIP) {
      accessAiService = await getAccessAiService(apiKey, serviceName);

      validateAccessAiService(accessAiService);

      validateApiMethods(accessAiService, method, originalURL, baseName);
    } else {
      accessAiService = await getServiceInfo(serviceName, method);
    }

    await validateMaxCountTpm(accessAiService);

    req.srtProxy = serviceName.toLowerCase().includes("srt");
    req.hasFile = isFileRequest(absolutePath, serviceName);

    if (method === "POST") {
      req.projectName = accessAiService.Service.Project.name;
      req.files = [];
    }

    req.requestType = accessAiService.Service.type;
    req.accessAiService = accessAiService;
    req.serviceName = serviceName;

    next();
  } catch (err) {
    next(err);
  }
};

/**
 * get api and method and originalUrl and baseName check is valid
 * @type {function}
 * @param {array} apis - list api for service
 * @param {string} method - method this route
 * @param {string} originalUrl - originalUrl this route
 * @param {string} baseName - baseName this route
 */
const checkValidApis = ({ apis, method, originalUrl, baseName }) => {
  let isValidApi = false;
  let files = [];

  // console.log(originalUrl);
  for (const api of apis) {
    if (
      originalUrl.includes(api.path) &&
      (api.method.includes(method) ||
        api.method.includes(method.toLowerCase())) &&
      path.basename(api.path) === baseName
    ) {
      isValidApi = true;
      api.files && (files = api.files);
      break;
    }
  }

  return { isValidApi, files };
};

/**
 * Checks the validity of services within an array of APIs and returns an array of files.
 *
 * @param {object} params - The parameters object.
 * @param {Array<object>} params.apis - An array of API objects to check for validity.
 * @returns {object} An object containing an array of files from valid services.
 */
const checkValidServices = ({ apis }) => {
  let files = [];
  for (const api of apis) {
    api.files && (files = api.files);
  }

  return { files };
};

/**
 * Adjusts the `originalUrl` and `baseName` based on the HTTP method.
 *
 * @param {string} originalUrl - The original URL.
 * @param {string} method - The HTTP method ('GET' or 'POST').
 * @returns {object} An object with adjusted `originalUrl`, `baseName`, `serviceName`, and `absolutePath`.
 */
const adjustServiceAndBaseName = (originalUrl, method) => {
  const urlSegments = originalUrl.split("/");
  const serviceName = urlSegments[2];
  let baseName = path.basename(originalUrl).split("?")[0];
  const urlLength = urlSegments.length;
  const absolutePath = urlSegments[3].split("?")[0];
  // absolutePath = urlSegments.pop().split('?')[0],
  let originalURL = null;

  // console.log(absolutePath, 'absolutePath', originalUrl, 'originalUrl', urlLength, baseName, 'baseName');

  // urlSegments.push(absolutePath)
  // const urlll = urlSegments.splice(2).join('/')

  // console.log(urlSegments, originalUrl, absolutePath,urlLength, baseName)

  if (method !== "GET") {
    // originalURL =
    // urlLength > 4 ? `${serviceName}/${absolutePath}` : originalUrl
    originalURL = `${serviceName}/${absolutePath}`;

    baseName = urlLength > 4 ? absolutePath : baseName;
  } else if (urlLength > 4 && isImageExtension(urlSegments)) {
    originalURL = `${serviceName}/*`;
    baseName = "*";
  } else {
    originalURL =
      urlLength > 4 ? `${serviceName}/${absolutePath}/*` : `${serviceName}/*`;
    baseName = "*";
  }
  console.log(
    absolutePath,
    "absolutePath",
    originalUrl,
    "originalUrl",
    urlLength,
    baseName,
    "baseName"
  );

  return { originalURL, baseName, serviceName, absolutePath };
};

/**
 * Checks if the given file extension represents an image.
 *
 * @param {string} fileExtension - The file extension to check.
 * @returns {boolean} True if the file extension represents an image, false otherwise.
 */
const isImageExtension = (url) => {
  const fileExtension = url.at(-1).split(".")[1];

  const imageExtensions = [
    ".jpg",
    ".jpeg",
    ".png",
    ".bmp",
    ".mp4",
    ".mkv",
    ".mov",
    ".avi",
    ".mpeg",
    ".wmv",
    ".webm",
    ".mp3",
    ".mp4",
    ".wav",
    ".ogg",
    ".flac",
  ];

  return imageExtensions.includes("." + fileExtension);
};

/**
 * Validate API methods for an AI service based on method, originalUrl, and baseName.
 *
 * @param {object} accessAiService - The AI service to validate API methods for.
 * @param {string} method - The HTTP method used (e.g., 'GET', 'POST').
 * @param {string} originalUrl - The original URL of the request.
 * @param {string} baseName - The base name of the request.
 * @throws {Error} Throws an error with a specific message if API method validation fails.
 */
const validateApiMethods = (accessAiService, method, originalUrl, baseName) => {
  const resultServiceApi = checkValidApis({
    apis: accessAiService.Service.apis,
    method,
    originalUrl,
    baseName,
  });

  // console.log(
  //   accessAiService.Service.apis,
  //   method,
  //   originalUrl,
  //   baseName,
  //   resultServiceApi,
  //   '***',
  // )

  // console.log(resultServiceApi);
  const validApiService = resultServiceApi.isValidApi;

  accessAiService.files = method === "POST" ? resultServiceApi.files : null;

  // console.log(resultServiceApi);
  if (!validApiService)
    throw global.error(404, messages.m.NOT_FOUND_ROUTE_ERROR);

  const resultApiKeyApi = checkValidApis({
    apis: accessAiService.apis,
    method,
    originalUrl,
    baseName,
  });

  const validApiApiKey = resultApiKeyApi.isValidApi;

  if (!validApiApiKey) throw global.error(400, messages.m.NOT_ALLOW_API);
};

/**
 * Retrieve access to an AI service based on the API key and service name.
 *
 * @param {string} apiKey - The API key for authentication.
 * @param {string} serviceName - The name of the service to access.
 * @returns {object} - The access to the AI service or throws an error if access is denied.
 * @throws {Error} Throws an error with a specific message if access is denied.
 */
const getAccessAiService = async (apiKey, serviceName) => {
  const accessAiService = await apisService.findAiServiceForApiKey({
    apiKey,
    serviceName,
  });

  if (!accessAiService)
    throw global.error(403, messages.m.INVALID_APIKEY_OR_SERVICE);

  return accessAiService;
};

/**
 * Validate the access AI service and throw errors if conditions are not met.
 *
 * @param {object} accessAiService - The access AI service object to validate.
 * @throws {Error} Throws an error with a specific message if validation fails.
 */
const validateAccessAiService = (accessAiService) => {
  if (accessAiService.ApiKey.maxCount < 1)
    throw global.error(400, messages.m.COUNT_API_KEY_ZERO);

  const dateNow = new Date();

  if (accessAiService.ApiKey.expireDate < dateNow)
    throw global.error(400, messages.m.API_KEY_EXPIRE);

  if (!accessAiService.ApiKey.isValid)
    throw global.error(400, messages.m.API_KEY_INVALID);
};

/**
 * Retrieve and prepare service information for a given service name and HTTP method.
 *
 * @param {string} serviceName - The name of the service to retrieve.
 * @param {string} method - The HTTP method used (e.g., 'GET', 'POST').
 * @returns {object} - The prepared service information including API data.
 */
const getServiceInfo = async (serviceName, method) => {
  const accessAiService = await servicesService.findService(serviceName);
  accessAiService.Service = accessAiService.dataValues;

  if (method === "POST") {
    const resultApiKeyApi = checkValidServices({
      apis: accessAiService.apis,
    });

    accessAiService.files = resultApiKeyApi.files;
  }

  return accessAiService;
};

/**
 * check file if need base64 or link save data
 * @type {function}
 * @function saveFile
 * @param {object} req - express request object
 * @param {object} res - express response object
 * @param {function} next - function that sends request to the next middleware
 */
export const saveFile = async (req, res, next) => {
  try {
    if (req.requestType === "proxy") {
      return next();
    }
    const { files } = req.accessAiService;
    if (files) {
      for (const file of files) {
        const { name, howGet, type, required } = file;
        if (howGet === "base64") {
          saveBase64({ req, type, required, name });
        }
        if (howGet === "link") {
          await saveLink({ req, name, required, next });
        }
      }
    }
    next();
  } catch (err) {
    next(err);
  }
};

/**
 * save base64 on disk
 * @type {function}
 * @function saveBase64
 * @param {object} req - express request object
 * @param {string} type - type of file
 * @param {boolean} required - check file has required
 * @param {string} name - name file in req body
 */
const saveBase64 = ({ req, type, required, name }) => {
  const dataBase64 = req.body[name];
  if (!dataBase64 && required)
    throw global.error(400, messages.m.NOT_FOUND_DATA_BASE64);

  if (dataBase64) {
    let extension = ".jpg";
    switch (type) {
      case "voice":
        extension = ".mp3";
        break;
      case "video":
        extension = ".mp4";
        break;
    }
    const fileName = `gateway-base64-${randomUUID()}${extension}`;
    const filePath = `${process.cwd()}/uploads/${fileName}`;
    const buffer = Buffer.from(dataBase64, "base64");
    const size = buffer.length;
    const readable = new Readable();

    readable.push(buffer);
    readable.push(null);
    readable.pipe(createWriteStream(filePath));

    req.files.push({
      originalname: fileName,
      path: filePath,
      encoding: "base64",
      mimeType: null,
      newName: fileName,
      oldName: fileName,
      size,
    });
  }
};

/**
 * get file link from req body, check link incorrect then save file in disk.
 * @type {function}
 * @function saveLink
 * @param {object} req - express request object
 * @param {string} type - type of file
 * @param {boolean} required - check file has required
 * @param {string} name - name file in req body
 */
const saveLink = async ({ req, name, required, next }) => {
  const url = req.body[name];
  if (!url && required) throw global.error(400, messages.m.REQUIRED_LINK);
  if (url) {
    const extension = extname(url);
    const axiosConfig = {
      responseType: "stream",
      method: "GET",
      url,
    };
    const response = await axios.sendRequest(axiosConfig);

    if (!response) {
      throw global.error(400, messages.m.NOT_GET_DATA_FROM_LINK);
    }

    const { data } = response;
    if (data) {
      const fileName = `gateway-link-${randomUUID()}${extension}`;
      const filePath = `${process.cwd()}/uploads/${fileName}`;
      const fileWriter = createWriteStream(filePath);
      const fileInfo = await writeFileStream({
        fileWriter,
        fileName,
        filePath,
        data,
      });
      req.files.push(fileInfo);
    }

    data.on("error", (error) => {
      next(error);
    });
  }
};

/**
 * create pipe, store data.
 * @type {function}
 * @function writeFileStream
 */
const writeFileStream = ({ data, fileWriter, fileName, filePath }) => {
  return new Promise((resolve, reject) => {
    try {
      data.pipe(fileWriter);
      fileWriter.on("finish", () => {
        const statsFile = statSync(filePath);
        resolve({
          originalname: fileName,
          path: filePath,
          encoding: "base64",
          mimeType: null,
          newName: fileName,
          oldName: fileName,
          size: statsFile.size,
        });
      });
    } catch (error) {
      reject(error);
    }
  });
};

/**
 * get count of tmp in one minute
 * @type {function}
 * @function getCountTmp
 * @returns countTpm
 */
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

/**
 * Define a function to check if the request has a file
 * @type {function}
 * @function isFileRequest
 * @returns true/false
 */
const isFileRequest = (absolutePath, serviceName) => {
  return (
    absolutePath.toLowerCase().includes("mp3") ||
    absolutePath.toLowerCase().includes("file") ||
    serviceName === "keywordsExtraction"
  );
};

/**
 * validates MaxCount Tpm
 * @type {function}
 * @function validateMaxCountTpm
 */
const validateMaxCountTpm = async (accessAiService) => {
  if (accessAiService.maxCount === 0) {
    throw global.error(400, messages.m.COUNT_API_MAX_COUNT_ZERO);
  }

  const { countTpm } = await getCountTmp(accessAiService);

  if (countTpm >= accessAiService.TPM)
    throw global.error(403, messages.m.MAX_COUNT_TPM_INVALID);
};

/**
 * checks ip of users
 * @type {function}
 * @function checkIp
 * @returns ip flag
 */
export const checkIp = (req, res, next) => {
  const ip =
    req.headers["x-forwarded-for"] === undefined
      ? req.ip.split(":").at(-1)
      : req.headers["x-forwarded-for"];
  let isAllowedIP = false;

  if (ip === process.env.REMOTE_IP) {
    isAllowedIP = true;
  }

  req.isAllowedIP = isAllowedIP;

  next();
};

/**
 * send file to server
 * @type {function}
 * @function proxyRequest
 * @param {object} req - express request object
 * @param {object} res - express response object
 * @param {function} next - function that sends request to the next middleware
 */
export const proxyRequest = async (req) => {
  try {
    const { url, agent } = buildRequestOptions(req);

    const beforSendTime = new Date();
    const files = req.files;

    req.storage = [];
    let data = req.body;
    let headers = null;

    if (files && files.length !== 0) {
      data = new FormData();
      for (const file of files) {
        const fileStream = createReadStream(file.path);
        data.append("file", fileStream);
        fileStream.on("end", async () => {
          await promises.unlink(file.path);
        });
      }
      headers = {
        ...data.getHeaders(),
      };
    } else if (!files) {
      data = null;
    }

    const config = {
      method: req.method,
      url,
      maxContentLength: Infinity,
      maxBodyLength: Infinity,
      headers,
      data,
      httpsAgent: agent,
    };

    const response = await axios.sendRequest(config);

    const aiTimeProcessing = Date.now() - beforSendTime;

    const result = response.data;
    return { result, aiTimeProcessing };
  } catch (error) {
    // next(error)
    return error;
  }
};

/**
 * Generates a Redis key based on the given API ID.
 *
 * @param {string} apiId - The API ID used to generate the Redis key.
 * @returns {string} The generated Redis key in the format "apiId::randomUUID".
 */
export const generateRedisKey = (apiId) => {
  return `${apiId}::${randomUUID()}`;
};

/**
 * Sets a value in Redis with the specified key and a default expiration time of 60 seconds.
 *
 * @param {string} key - The key under which to store the value in Redis.
 * @param {*} value - The value to store in Redis.
 * @returns {Promise<void>} A promise that resolves when the value is successfully set in Redis.
 */
export const setRedisKey = (key, value) =>
  utils.redisSetKey({ key, seconds: 60, value });

/**
 * Updates the counts for API keys and APIs, decrementing the maximum count if a value is provided.
 *
 * @param {string|null} value - The value associated with the API key (null if none).
 * @param {string} apiId - The ID of the API to update.
 * @returns {Promise<void>} A promise that resolves when the counts are updated.
 */
export const updateCounts = async (value, apiId) => {
  if (value) {
    // Decrement the maximum count for the API key if a value is provided.
    await apiKeysService.updateOne(
      { maxCount: sequelize.literal("max_count - 1") },
      { value }
    );
  }

  // Decrement the maximum count for the specified API.
  await apisService.updateOne(
    { maxCount: sequelize.literal("max_count - 1") },
    { id: apiId }
  );
};

/**
 * Build and return request options for making HTTP requests.
 *
 * @param {object} req - The incoming request object.
 * @returns {object} - The request options object for the HTTP request.
 */
export const buildRequestOptions = (req) => {
  const { host, protocol, rejectUnauthorized } = req.accessAiService.Service;
  const baseName = req.originalUrl.split("/").slice(3).join("/");
  const url = `${protocol}://${host}/${baseName}`;

  let agent = null;

  if (!rejectUnauthorized && protocol === "https") {
    agent = new https.Agent({
      rejectUnauthorized: false,
    });
  }

  // console.log(url)
  return { url, agent };
};

/**
 * Handles a non-file request by setting the appropriate request options and making the HTTP request.
 *
 * @param {IncomingMessage} req - The incoming request object.
 * @param {ServerResponse} res - The server response object.
 * @param {string} apiId - The identifier of the API.
 * @param {string} value - The API key value for authentication.
 * @param {Array} gatewayLogData - An array to log gateway data (optional).
 */
export const handleNonFileRequest = async (
  req,
  res,
  apiId,
  value,
  gatewayLogData
) => {
  try {
    // console.log('req.headers.authorization');
    // delete req.headers.apikey
    const requestOptions = buildRequestOptions(req);

    requestOptions.method = req.method;
    // requestOptions.authorization = req.headers.authorization
    const beforeSendTime = new Date();

    if (req.method !== "GET") {
      requestOptions.headers = {
        Authorization: req.headers.authorization,
        "Content-Type": "application/json", // Set the content type to JSON
      };

      requestOptions.body = JSON.stringify(req.body);
    }

    request(requestOptions, async (_error, _response, body) => {
      const aiTimeProcessing = Date.now() - beforeSendTime;

      const resultCode = _response.statusCode;

      if (req.method === "POST") {
        gatewayLogData.push({ aiTimeProcessing });
      }

      const redisKey = generateRedisKey(apiId);
      setRedisKey(redisKey, new Date());

      const result = JSON.parse(body);

      if (resultCode >= 200 && resultCode < 300) {
        await updateCounts(value, apiId);
      }

      if (result.status) {
        // const data = replaceFilePathWithRequestedUrlPart(result, req.serviceName)
        return res.json(result);
      } else {
        return responseMessage({
          res,
          statusCode: resultCode,
          data: result,
        });
      }
    });
  } catch (error) {
    console.log(error);
  }
};

// /**
//  * Replace a part of a file path in the result data with a part from the requested URL.
//  *
//  * @param {object} result - The result data containing the file path to be modified.
//  * @param {object} req - The request object containing information to replace with.
//  * @param {string} req.serviceName - The part from the requested URL to use for replacement.
//  * @returns {object} The modified result data with the replaced file path.
//  */
// const replaceFilePathWithRequestedUrlPart = (result, serviceName) => {
//   if (!result.data || !result.data.filePath) {
//     return result // Return data as is if inputs are missing
//   }
//   // Extract the desired replacement part from the requested URL
//   const replacement = result.data.filePath.split('/')[2]

//   result.data.filePath = result.data.filePath.replace(
//     replacement,
//     serviceName,
//   )

//   return result
// }

/**
 * Handles a file request by setting up a proxy and forwarding the response.
 *
 * @param {IncomingMessage} req - The incoming request object.
 * @param {ServerResponse} res - The server response object.
 * @param {string} apiId - The ID of the API being accessed.
 * @param {string} value - The value associated with the API key.
 * @param {Array} gatewayLogData - An array to log gateway data (optional).
 */
export const handleFileRequest = async (
  req,
  res,
  apiId,
  value,
  gatewayLogData
) => {
  const requestOptions = buildRequestOptions(req);

  const proxy = request(requestOptions);
  const beforeSendTime = new Date();

  proxy
    .on("response", async (proxyResponse) => {
      const resultCode = proxyResponse.statusCode;

      const aiTimeProcessing = Date.now() - beforeSendTime;
      if (req.method === "POST") {
        gatewayLogData.push({ aiTimeProcessing });
      }

      const redisKey = generateRedisKey(apiId);
      setRedisKey(redisKey, new Date());

      if (resultCode >= 200 && resultCode < 300) {
        await updateCounts(value, apiId);
      }
    })
    .pipe(res);

  req.pipe(proxy);
};

/**
 * Handles an SRT proxy request by forwarding the request and processing the response.
 *
 * @param {IncomingMessage} req - The incoming request object.
 * @param {ServerResponse} res - The server response object.
 * @param {string} apiId - The ID of the API being accessed.
 * @param {string} value - The value associated with the API key.
 * @param {Array} gatewayLogData - An array to log gateway data (optional).
 */
export const handleSRTProxyRequest = async (
  req,
  res,
  apiId,
  value,
  gatewayLogData
) => {
  const { result } = await proxyRequest(req);
  const beforeSendTime = new Date();

  const aiTimeProcessing = Date.now() - beforeSendTime;
  if (req.method === "POST") {
    gatewayLogData.push({ aiTimeProcessing });
  }

  const redisKey = generateRedisKey(apiId);
  setRedisKey(redisKey, new Date());

  if (result.status === "success") {
    await updateCounts(value, apiId);
  }
  res.json(result);
};
```