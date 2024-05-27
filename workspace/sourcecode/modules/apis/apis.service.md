
```javascript



import ServiceBase from "../../services/serviceBase.js";
import models from "../models.js";

/**
 * Class representing organizations apis
 * @class
 */
export default new (class Api extends ServiceBase {
  /**
   * find api with its service and apiKey
   * @type {function}
   * @param {object}
   * @public
   */
  async findApiKeyService(clause) {
    return await this.model.findAll({
      where: clause,
      include: [{ model: models.Service }, { model: models.ApiKey }],
    });
  }

  // /**
  //  * find api with its service and apiKey
  //  * @type {function}
  //  * @param {object}
  //  * @public
  //  */
  //  async findService(name) {
  //   return await this.model.findOne({ where: { name } })

  //   // return await this.model.findOne({ where: { username } })
  // }

  /**
   * find all apis
   * @type {function}
   * @param {object}
   * @public
   */
  async findAllApis(clause, limit, offs) {
    let offset;
    if (offs) {
      offset = (Number(offs) - 1) * limit;
    } else {
      offset = null;
    }
    return await this.model.findAll({
      where: clause,
      include: [{ model: models.Service }, { model: models.ApiKey }],
      order: [["created_at", "DESC"]],
      limit,
      offset,
    });
  }

  /**
   * find and counts all permissions for one role
   * @type {function}
   * @param {object}
   * @public
   */
  async findAiServiceForApiKey(clause) {
    return await this.model.findOne({
      include: [
        {
          model: models.Service,
          where: {
            name: clause.serviceName,
          },
          include: [
            {
              model: models.Project,
            },
          ],
        },
        {
          model: models.ApiKey,
          where: {
            value: clause.apiKey,
          },
        },
      ],
    });
  }
})(models.Api);
```