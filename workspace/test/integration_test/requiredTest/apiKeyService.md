import ServiceBase from '../../../src/services/serviceBase'
import models from '../../../src/modules/models'
import { randomUUID } from 'crypto'
import utils from '../../../src/services/utils'


/**
 * Class representing organizations services
 * @class
 */
export default new (class Service extends ServiceBase {
  /**
   * create mock user for test
   * @type {function}
   * @param {object}
   * @public
   */
  async create(info) {
    const { expireDate } = info,
      apiKeyValue = randomUUID(),
      expire_date = utils.afterTime(expireDate)

    const insertedProject = await this.model.create({
      ...info,
      value: apiKeyValue,
      expireDate: expire_date,
    })

    return insertedProject.dataValues.id
  }

  /**
   * delete mock user for test
   * @type {function}
   * @param {object}
   * @public
   */
  async delete(userId) {
    await this.model.destroy({
      where: { id: userId },
      force: true
    })
  }
})(models.ApiKey)
