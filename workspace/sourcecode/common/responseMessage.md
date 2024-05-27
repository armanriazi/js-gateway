```javascript
import logger from "../services/logger.js";
import env from "./env.js";
import backendLogger from "../services/backendLogger.js";

/**
 * @function responseMessage
 * @description gets environment variable's value
 * @param {object} res - express response object
 * @param {number} statusCode - user response status code
 * @param {string} status - response status (success or error)
 * @param {object | string} data - server's processing result
 * @param {string} errStack - err object stack
 */
export default ({
  res,
  statusCode = 200,
  status = "success",
  meta,
  data,
  errStack,
}) => {
  if (statusCode > 399) {
    status = "error";
  }

  backendLogger.responseLog({ res, data });

  statusCode === 500 && logger({ errStack });

  res.status(statusCode).json({
    data: {
      status,
      message: data,
    },
    meta,
    ...(env("NODE_ENV") === "development" ? { errStack } : {}),
  });
};
```