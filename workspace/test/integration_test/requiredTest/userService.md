import ServiceBase from '../../../src/services/serviceBase'
import models from '../../../src/modules/models'
import auth from '../../../src/services/auth'
import bcrypt from '../../../src/common/helper/bcrypt'

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
    const insertedUser = await this.model.create({
      ...info,
    })

    return insertedUser.dataValues.id
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
      force: true,
    })
  }

  /**
   * delete mock user for test
   * @type {function}
   * @param {object}
   * @public
   */
  async login(info) {
    const { password, username } = info
    const user = await this.model.findOne({ where: { username } })
    if (!user) {
      console.log('user not found')
      return
    }

    const storedPass = user.dataValues.password

    if (!(await bcrypt.compareHashPass(password, storedPass)))
      console.log('not correct')

    const token = await auth.generateToken({ ...user.dataValues })

    return token
  }
})(models.User)
