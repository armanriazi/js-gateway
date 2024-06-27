
import ServiceBase from "../../services/serviceBase.js";
import models from "../models.js";

import ServiceBase from '../../services/serviceBase'
import models from '../models'


/**
 * Class representing organizations services
 * @class
 */
export default new (class Service extends ServiceBase {
  /**
   * find a service and its project
   * @type {function}
   * @param {object}
   * @public
   */
  async findProjectService(clause) {
    return await this.model.findAll({
      where: clause,
      include: [{ model: models.Project }],

    });

    })

  }

  /**
   * find api with its service and apiKey
   * @type {function}
   * @param {object}
   * @public
   */

  async findService(name) {

   async findService(name) {

    return await this.model.findOne({
      where: { name },
      include: [
        {
          model: models.Project,
        },
      ],

    });

    })


    // return await this.model.findOne({ where: { username } })
  }

  /**
   * find all services and their projects
   * @type {function}
   * @param {object}
   * @public
   */
  async findAllServices(clause, limit, offs) {

    let offset;
    if (offs) {
      offset = (Number(offs) - 1) * limit;
    } else {
      offset = null;

    let offset
    if (offs) {
      offset = (Number(offs) - 1) * limit
    } else {
      offset = null

    }
    return await this.model.findAll({
      where: clause,
      include: [{ model: models.Project }],

      order: [["created_at", "DESC"]],
      limit,
      offset,
    });
  }
})(models.Service);

      order: [['created_at', 'DESC']],
      limit,
      offset,
    })
  }
})(models.Service)

