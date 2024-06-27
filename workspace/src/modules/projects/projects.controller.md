
import service from "./projects.service.js";
import ControllerBase from "../../services/controllerBase.js";
import responseMessage from "../../common/responseMessage.js";
import messages from "../../common/messages.js";
import utility from "../../common/helper/utility.js";

import service from './projects.service'
import ControllerBase from '../../services/controllerBase'
import responseMessage from '../../common/responseMessage'
import messages from '../../common/messages'
import utility from '../../common/helper/utility'


/**
 * representive class of projects controllers
 * @class
 */
export default new (class Controller extends ControllerBase {
  /**
   * @function index
   * @description indexes all projects matching input filters
   * @param {object} req - express request object
   * @param {object} res - express response object
   * @param {function} next - function that sends request to the next middleware
   */
  index = async (req, res, next) => {
    try {

      const { startTimeStamp, endTimeStamp, limit, offs } = req.query;
      const clause = utility.filterQueryBuilder({
        startTimeStamp,
        endTimeStamp,
      });
      const documents = await this.service.findAllProjects(clause, limit, offs);

      const { startTimeStamp, endTimeStamp, limit, offs } = req.query,
        clause = utility.filterQueryBuilder({
          startTimeStamp,
          endTimeStamp,
        }),
        documents = await this.service.findAllProjects(clause, limit, offs)

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


  /**
   * @function updateProject
   * @description updates a project
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  updateProject = async (req, res, next) => {
    try {

      const storedProject = await this.service.findOne({ ...req.params });

      if (!storedProject) {
        throw global.error(400, messages.m.PROJECT_NOT_FOUND);

      const storedProject = await this.service.findOne({ ...req.params })

      if (!storedProject) {
        throw global.error(400, messages.m.PROJECT_NOT_FOUND)

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


  /**
   * @function getProject
   * @description find a specific row in a table
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  getProject = async (req, res, next) => {
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

