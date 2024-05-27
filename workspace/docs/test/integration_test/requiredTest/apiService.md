import ServiceBase from '../../../src/services/serviceBase'
import models from '../../../src/modules/models'

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
    const insertedProject = await this.model.bulkCreate([
      ...info,
    ])

    return insertedProject[0].dataValues.id
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
})(models.Api)
