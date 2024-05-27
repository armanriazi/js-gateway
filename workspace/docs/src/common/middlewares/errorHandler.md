
```javascript



import responseMessage from "../../common/responseMessage.js";
import messages from "../../common/messages.js";
import utils from "../../services/utils.js";
import logger from "../../services/backendLogger.js";

/**
 * @function errorHandler
 * @description error handler middleware for catching errors ocurred in app controllers
 * @param {string} err - ocurred error in controllers
 * @param {object} req - express request object
 * @param {object} res - express response object
 * @param {function} next - function that sends request to the next middleware
 */
// eslint-disable-next-line no-unused-vars
export default async (err, req, res, next) => {
  utils.deleteFile(req.files);
  const { name, status, message } = err;
  const sequelizeErrorDisptcher = {
    SequelizeUniqueConstraintError: messages.m.UNIQUE_CONSTRAINT_ERROR,
    SequelizeForeignKeyConstraintError: messages.m.FOREIGN_KEY_CONSTRAINT_ERROR,
  };

  logger.errorLog({ res, err });
```