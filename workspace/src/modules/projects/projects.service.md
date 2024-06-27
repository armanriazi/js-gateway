
import ServiceBase from "../../services/serviceBase.js";
import models from "../models.js";

import ServiceBase from '../../services/serviceBase'
import models from '../models'


/**
 * Class representing organizations projects
 * @class
 */
export default new (class Project extends ServiceBase {
  /**
   * find all projects with where clause
   * @type {function}
   * @param {object}
   * @public
   */
  async findProjectService(clause) {
    return await this.model.findAll({
      where: clause,
      include: [{ model: models.Service }],

    });

    })

  }

  /**
   * find all projects with pagination
   * @type {function}
   * @param {object}
   * @public
   */
  async findAllProjects(clause, limit, offs) {

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
      include: [{ model: models.Service }],

      order: [["created_at", "DESC"]],
      limit,
      offset,
    });
  }
})(models.Project);

      order: [['created_at', 'DESC']],
      limit,
      offset,
    })
  }
})(models.Project)

