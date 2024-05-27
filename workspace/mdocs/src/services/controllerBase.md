
```javascript



import responseMessage from "../common/responseMessage.js";

/**
 * representive class of basic controller for all resources
 * @class
 */
export default class controllerBase {
  constructor(service) {
    this.service = service;
  }

  /**
   * @function create
   * @description create a new row in DB
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  create = async (req, res, next) => {
    try {
      const document = await this.service.create({
        ...req.params,
        ...req.body,
      });

      responseMessage({
        res,
        statusCode: 201,
        data: document,
      });
    } catch (error) {
      next(error);
    }
  };

  /**
   * @function index
   * @description list of rows of a table
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  index = async (req, res, next) => {
    try {
      const { limit, offset } = req.query;
      const documents = await this.service.findAndCount({
        where: { ...req.params },
        pagination: { limit, offset },
      });

      responseMessage({
        res,
        data: documents,
      });
    } catch (error) {
      next(error);
    }
  };

  /**
   * @function indexWithDeleted
   * @description list of rows of a table indlude deleted entries
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  indexWithDeleted = async (req, res, next) => {
    try {
      const { limit, offset } = req.query;
      const documents = await this.service.findAndCount({
        where: { ...req.params },
        pagination: { limit, offset },
        paranoid: false,
      });

      responseMessage({
        res,
        data: documents,
      });
    } catch (error) {
      next(error);
    }
  };

  /**
   * @function get
   * @description find a specific row in a table
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  get = async (req, res, next) => {
    try {
      const document = await this.service.findOne({ ...req.params });

      responseMessage({
        res,
        data: document,
      });
    } catch (error) {
      next(error);
    }
  };

  /**
   * @function update
   * @description update a specific row in a table
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  update = async (req, res, next) => {
    try {
      const updatedDocument = await this.service.updateOne(req.body, {
        ...req.params,
      });

      responseMessage({
        res,
        data: updatedDocument,
      });
    } catch (error) {
      console.log(error.message);
      next(error);
    }
  };

  /**
   * @function remove
   * @description remove a specific row in a table
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  remove = async (req, res, next) => {
    try {
      const deletedDocument = await this.service.deleteOne({
        where: { ...req.params },
      });

      responseMessage({
        res,
        data: deletedDocument,
      });
    } catch (error) {
      next(error);
    }
  };

  /**
   * @function restore
   * @description restore a specific row in a table
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  restore = async (req, res, next) => {
    try {
      const restoredDocument = await this.service.restoreOne({ ...req.params });

      responseMessage({
        res,
        data: restoredDocument,
      });
    } catch (error) {
      next(error);
    }
  };
}
```