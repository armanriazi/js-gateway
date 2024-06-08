
import service from "./services.service.js";
import ControllerBase from "../../services/controllerBase.js";
import responseMessage from "../../common/responseMessage.js";
import messages from "../../common/messages.js";
import utility from "../../common/helper/utility.js";

import service from './services.service'
import ControllerBase from '../../services/controllerBase'
import responseMessage from '../../common/responseMessage'
import messages from '../../common/messages'
import utility from '../../common/helper/utility'
>>>>>>> origin/armanriazi

/**
 * representive class of projects controllers
 * @class
 */
export default new (class Controller extends ControllerBase {
  /**
   * @function create
   * @description creates a new service
   * @param {string} name - service's name
   * @param {string} ProjectId - service's ProjectId
   * @param {string} validation - service's validation
   * @param {object} apis - service's apis
   * @param {object} destination - service's destination
   * @param {object} type - service's type
   * @param {object} queue - service's queue
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */

  create = async (req, res, next) => {
    try {
      const { type } = req.body;

      if (type === "proxy") {
        req.body.queue = "null";

   create = async (req, res, next) => {
    try {
      const { type } = req.body

      if (type === 'proxy') {
        req.body.queue = 'null'
>>>>>>> origin/armanriazi
      }

      const insertedService = await this.service.create({
        ...req.body,

      });

      })
>>>>>>> origin/armanriazi

      responseMessage({
        res,
        statusCode: 201,
        data: insertedService.dataValues,

      });
    } catch (error) {
      next(error);
    }
  };

      })
    } catch (error) {
      next(error)
    }
  }
>>>>>>> origin/armanriazi

  /**
   * @function index
   * @description indexes all services matching input filters
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
      const documents = await this.service.findAllServices(clause, limit, offs);

      const { limit, offs, startTimeStamp, endTimeStamp } = req.query,
        clause = utility.filterQueryBuilder({
          startTimeStamp,
          endTimeStamp,
        }),
        documents = await this.service.findAllServices(clause, limit, offs)
>>>>>>> origin/armanriazi

      responseMessage({
        res,
        data: documents,

      });
    } catch (error) {
      next(error);
    }
  };

      })
    } catch (error) {
      next(error)
    }
  }
>>>>>>> origin/armanriazi

  /**
   * @function updateService
   * @description updates a service
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  updateService = async (req, res, next) => {
    try {

      const storedService = await this.service.findOne({ ...req.params });
      const serviceName = await this.service.findOne({ name: req.body.name });

      if (!storedService) {
        throw global.error(400, messages.m.SERVICE_NOT_FOUND);
      }
      if (serviceName) {
        throw global.error(400, messages.m.DUPLICATE_SERVICE_NAME);

      const storedService = await this.service.findOne({ ...req.params }),
        serviceName = await this.service.findOne({ name: req.body.name })

      if (!storedService) {
        throw global.error(400, messages.m.SERVICE_NOT_FOUND)
      }
      if (serviceName) {
        throw global.error(400, messages.m.DUPLICATE_SERVICE_NAME)
>>>>>>> origin/armanriazi
      }

      const updatedDocument = await this.service.updateOne(
        {
          ...req.body,
        },
        {
          id: req.params.id,
        }

      );
      responseMessage({
        res,
        data: updatedDocument,
      });
    } catch (error) {
      next(error);
    }
  };

      )
      responseMessage({
        res,
        data: updatedDocument,
      })
    } catch (error) {
      next(error)
    }
  }
>>>>>>> origin/armanriazi

  /**
   * @function getService
   * @description find a specific row in a table
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  getService = async (req, res, next) => {
    try {

      const { id } = req.params;

      const apiKey = await this.service.findProjectService({ id });
      responseMessage({
        res,
        data: apiKey,
      });
    } catch (error) {
      next(error);
    }
  };
})(service);

      const { id } = req.params

      const apiKey = await this.service.findProjectService({ id })
      responseMessage({
        res,
        data: apiKey,
      })
    } catch (error) {
      next(error)
    }
  }
})(service)
>>>>>>> origin/armanriazi
