
```javascript



import apiKeysService from "./apiKeys.service.js";
import ControllerBase from "../../services/controllerBase.js";
import responseMessage from "../../common/responseMessage.js";
import messages from "../../common/messages.js";
import crypto from "crypto";
import utils from "../../services/utils.js";
import utility from "../../common/helper/utility.js";

/**
 * representive class of users controllers
 * @class
 */
export default new (class Controller extends ControllerBase {
  /**
   * @function create
   * @description creates a new apikey
   * @param {object} req.body.UserId - user's id
   * @param {object} req.body.expireDate - apiKey's expireDate
   * @param {object} req.body.maxCount - apikey's maxCount
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  create = async (req, res, next) => {
    try {
      // generate 64-bit apiKey
      const randomApiKey = crypto.randomBytes(64).toString("hex");

      const { expireDate } = req.body;
      const apiKeyValue = randomApiKey;
      const expire_date = utils.afterTime(expireDate);

      const apiKey = await this.service.create({
        ...req.body,
        value: apiKeyValue,
        expireDate: expire_date,
      });

      responseMessage({
        res,
        statusCode: 201,
        data: apiKey,
      });
    } catch (error) {
      next(error);
    }
  };

  /**
   * @function update
   * @description updates an apikey
   * @param {string} id - apiKey's id
   * @param {boolean} isValid - apiKey's validation
   * @param {number} maxCount - apiKey's maxCount
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  update = async (req, res, next) => {
    try {
      const { expireDate } = req.body;
      let expire_date;

      if (expireDate) {
        expire_date = utils.afterTime(expireDate);
        req.body.expireDate = expire_date;
      }

      const updatedApiKey = await this.service.updateOne(
        {
          ...req.body,
        },
        {
          id: req.params.id,
        }
      );
      responseMessage({
        res,
        data: updatedApiKey,
      });
    } catch (error) {
      next(error);
    }
  };

  /**
   * @function index
   * @description indexes all apiKeys matching input filters
   * @param {object} req - express request object
   * @param {object} res - express response object
   * @param {function} next - function that sends request to the next middleware
   */
  index = async (req, res, next) => {
    try {
      const { limit, offs, startTimeStamp, endTimeStamp } = req.query;
      const clause = utility.filterQueryBuilder({
        startTimeStamp,
        endTimeStamp,
      });
      const documents = await this.service.findAllApiKey(clause, limit, offs);

      responseMessage({
        res,
        data: documents,
      });
    } catch (error) {
      next(error);
    }
  };

  /**
   * @function getApiKey
   * @description find a specific row in a table
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  getApiKey = async (req, res, next) => {
    try {
      const UserId = req.user.role === "user" ? req.user.id : null;
      const apiKeyId = req.params.id;
      const clause = {
        id: apiKeyId,
        UserId,
      };

      const document = await this.service.findApiKey(clause);

      if (document.length === 0) {
        throw global.error(
          400,
          `${messages.c.APIKEY_ID} ${messages.m.NOT_FOUND}`
        );
      }

      responseMessage({
        res,
        data: document,
      });
    } catch (error) {
      next(error);
    }
  };
})(apiKeysService);
```