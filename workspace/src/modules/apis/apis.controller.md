
```javascript



import service from "./apis.service.js";
import ControllerBase from "../../services/controllerBase.js";
import responseMessage from "../../common/responseMessage.js";
import utility from "../../common/helper/utility.js";

/**
 * representive class of projects controllers
 * @class
 */
export default new (class Controller extends ControllerBase {
  /**
   * @function create
   * @description creates a new api
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  create = async (req, res, next) => {
    try {
      const api = await this.service.bulkCreate([...req.body]);

      responseMessage({
        res,
        statusCode: 201,
        data: api,
      });
    } catch (error) {
      next(error);
    }
  };

  /**
   * @function index
   * @description indexes all projects matching input filters
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
      const documents = await this.service.findAllApis(clause, limit, offs);

      responseMessage({
        res,
        data: documents,
      });
    } catch (error) {
      next(error);
    }
  };

  /**
   * @function getApiKeyService
   * @description find a specific row in a table based on id
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  getApiKeyService = async (req, res, next) => {
    try {
      const { id } = req.params;

      const apiKey = await this.service.findApiKeyService({ id });

      responseMessage({
        res,
        data: apiKey,
      });
    } catch (error) {
      next(error);
    }
  };
})(service);
```