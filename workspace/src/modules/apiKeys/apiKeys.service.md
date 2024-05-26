
```javascript



import ServiceBase from "../../services/serviceBase.js";
import models from "../models.js";

/**
 * Class representing organizations apiKey
 * @class
 */
export default new (class ApiKey extends ServiceBase {
  /**
   * find one apiKey with the services is been accessed to it
   * @type {function}
   * @param {object}
   * @public
   */
  async findApiKey(clause) {
    if (clause.UserId === null) delete clause.UserId;
    return await this.model.findAll({
      where: clause,
      attributes: {
        exclude: ["password"],
      },
      include: [{ model: models.Api, include: { model: models.Service } }],
    });
  }

  /**
   * find all apiKeys with the services is been accessed to them
   * @type {function}
   * @param {object}
   * @public
   */
  async findAllApiKey(clause, limit, offs) {
    let offset;
    if (offs) {
      offset = (Number(offs) - 1) * limit;
    } else {
      offset = null;
    }
    return await this.model.findAll({
      where: clause,
      attributes: {
        exclude: ["password"],
      },
      include: [{ model: models.Api, include: { model: models.Service } }],
      order: [["created_at", "DESC"]],
      offset,
      limit,
    });
  }
})(models.ApiKey);
```