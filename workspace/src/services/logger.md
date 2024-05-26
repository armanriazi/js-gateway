
```javascript



import crypto from "crypto";
import winston from "../common/helper/winston.js";
import logger from "./backendLogger.js";

/**
 * @function logger
 * @description logs incoming request and outgoing response and server errors
 * @param {object} req - express request object
 * @param {object} res - express response object
 * @param {object} responseBody - response data object sent to user
 * @param {string} errStack - server error stack
 */
export default async ({ req, res, responseBody, errStack }) => {
  try {
    if (errStack) {
      // send to logger service
      logger.errorLog({ res, error: errStack });
    }

    const requestId = res.locals.requestId || crypto.randomUUID();
    const log = {
      requestId,
      meta: {},
      data: {},
    };

    if (!res.locals.requestId) {
      const { body, headers, method, originalUrl, path, file, files, ip } = req;

      log.meta = {
        userAgent: headers["user-agent"],
        ip,
        method,
        originalUrl,
        path,
      };

      if (!path.includes("login") && !path.includes("password")) {
        log.data.request = { body, file, files };
      }
    }

    if (!responseBody && res.locals.requestId && (req.file || req.files)) {
      const { body, file, files } = req;

      log.data.request = { body, file, files };
    }

    responseBody && responseBody.data.token && delete responseBody.data.token;

    responseBody && (log.data.response = responseBody);

    // send to logger service

    res.locals.requestId = requestId;
  } catch (err) {
    winston.error(err.message, err.stack);
  }
};
```