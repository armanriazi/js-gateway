
import ServiceBase from "../../services/serviceBase.js";
import models from "../models.js";

import ServiceBase from '../../services/serviceBase'
import models from '../models'


/**
 * Class representing organizations user
 * @class
 */
export default new (class User extends ServiceBase {
  /**
   * find all apikeys for one user
   * @type {function}
   * @param {object}
   * @public
   */
  async findUserApiKey(clause) {
    return await this.model.findAll({
      where: clause,
      attributes: {

        exclude: ["password"],

        exclude: ['password'],

      },
      include: [
        {
          model: models.ApiKey,
          include: { model: models.Api, include: { model: models.Service } },
        },
      ],

    });

    })

  }

  /**
   * find all users and its apikeys apiKey
   * @type {function}
   * @param {object}
   * @public
   */
  async findAllUsersApiKey(clause, role, limit, offs) {

    if (role !== null) clause = { role };

    let offset;
    if (offs) {
      offset = (Number(offs) - 1) * limit;
    } else {
      offset = null;

    if (role !== null) clause = { role }

    let offset
    if (offs) {
      offset = (Number(offs) - 1) * limit
    } else {
      offset = null

    }

    return await this.model.findAll({
      where: clause,
      attributes: {

        exclude: ["password"],

        exclude: ['password'],

      },
      include: [
        {
          model: models.ApiKey,
          include: { model: models.Api, include: { model: models.Service } },
        },
      ],

      order: [["created_at", "DESC"]],
      offset,
      limit,
    });

      order: [['created_at', 'DESC']],
      offset,
      limit,
    })

  }

  /**
   * find one user and its role
   * @type {function}
   * @param {object}
   * @public
   */
  async findUserRole(id) {
    return await this.model.findAll({
      where: {
        id,
      },

    });
  }
})(models.User);

    })
  }
})(models.User)

