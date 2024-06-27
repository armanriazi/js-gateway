
import service from "./users.service.js";
import ControllerBase from "../../services/controllerBase.js";
import responseMessage from "../../common/responseMessage.js";
import messages from "../../common/messages.js";
import bcrypt from "../../common/helper/bcrypt.js";
import auth from "../../services/auth.js";
import utility from "../../common/helper/utility.js";

import service from './users.service'
import ControllerBase from '../../services/controllerBase'
import responseMessage from '../../common/responseMessage'
import messages from '../../common/messages'
import bcrypt from '../../common/helper/bcrypt'
import auth from '../../services/auth'
import utility from '../../common/helper/utility'


/**
 * representive class of users controllers
 * @class
 */
export default new (class Controller extends ControllerBase {
  /**
   * @function create
   * @description creates a new user
   * @param {string} password - user's password
   * @param {string} username - user's username
   * @param {string} role - user's role
   * @param {object} profile - user's profile
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  create = async (req, res, next) => {
    try {
      const insertedUser = await this.service.create({
        ...req.body,

      });

      delete insertedUser.dataValues.password;

      })

      delete insertedUser.dataValues.password


      responseMessage({
        res,
        statusCode: 201,
        data: insertedUser.dataValues,

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
   * @function updatePassword
   * @description updates user's password
   * @param {string} newPassword - user's new password
   * @param {string} confirmNewPassword - confirm of user's new password
   * @param {string} currentPassword - user's current password
   * @param {function} next - function that send request to the next middleware
   */
  updatePassword = async (req, res, next) => {
    try {

      const { currentPassword, newPassword, confirmNewPassword } = req.body;

      if (confirmNewPassword !== newPassword) {
        throw global.error(400, messages.m.INCORRECT_CONFIRM_PASSWORD);
      }

      const user = await this.service.findOne({ ...req.params });

      if (
        !user ||
        (req.user.role !== "superAdmin" &&
          user.dataValues.role === "admin" &&
          user.dataValues.id !== req.user.id)
      ) {
        throw global.error(400, `${messages.c.USER} ${messages.m.NOT_FOUND}`);
      }

      const { password } = user.dataValues;

      if (!(await bcrypt.compareHashPass(currentPassword, password))) {
        throw global.error(400, messages.m.INVALID_LOGIN);

      const { currentPassword, newPassword, confirmNewPassword } = req.body

      if (confirmNewPassword !== newPassword) {
        throw global.error(400, messages.m.INCORRECT_CONFIRM_PASSWORD)
      }

      const user = await this.service.findOne({ ...req.params })

      if (
        !user ||
        (req.user.role !== 'superAdmin' &&
          user.dataValues.role === 'admin' &&
          user.dataValues.id !== req.user.id)
      ) {
        throw global.error(400, `${messages.c.USER} ${messages.m.NOT_FOUND}`)
      }

      const { password } = user.dataValues

      if (!(await bcrypt.compareHashPass(currentPassword, password))) {
        throw global.error(400, messages.m.INVALID_LOGIN)

      }

      const updatedDocument = await this.service.updateOne(
        { password: newPassword },
        {
          ...req.params,
        }

      );

      )


      responseMessage({
        res,
        data: updatedDocument,

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
   * @function index
   * @description indexes all users matching input filters
   * @param {object} req - express request object
   * @param {object} res - express response object
   * @param {function} next - function that sends request to the next middleware
   */
  index = async (req, res, next) => {
    try {

      const { startTimeStamp, endTimeStamp, limit, offs } = req.query;
      const role = req.user.role === "superAdmin" ? null : "user";
      const clause = utility.filterQueryBuilder({
        startTimeStamp,
        endTimeStamp,
      });
      const documents = await this.service.findAllUsersApiKey(
        clause,
        role,
        limit,
        offs
      );

      let { startTimeStamp, endTimeStamp, limit, offs } = req.query,
        role = req.user.role === 'superAdmin' ? null : 'user',
        clause = utility.filterQueryBuilder({
          startTimeStamp,
          endTimeStamp,
        }),
        documents = await this.service.findAllUsersApiKey(
          clause,
          role,
          limit,
          offs
        )


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
   * @function login
   * @description login with user info
   * @param {object} password - user's password
   * @param {object} username - user's username
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  login = async (req, res, next) => {
    try {

      const { password, username } = req.body;

      const user = await this.service.findOne({ username });
      if (!user) {
        throw global.error(400, messages.m.USER_NOT_FOUND);
      }

      const storedPass = user.dataValues.password;

      if (!(await bcrypt.compareHashPass(password, storedPass)))
        throw global.error(400, messages.m.INVALID_LOGIN);

      const token = await auth.generateToken({ ...user.dataValues });

      const { password, username } = req.body

      const user = await this.service.findOne({ username })
      if (!user) {
        throw global.error(400, messages.m.USER_NOT_FOUND)
      }

      const storedPass = user.dataValues.password

      if (!(await bcrypt.compareHashPass(password, storedPass)))
        throw global.error(400, messages.m.INVALID_LOGIN)

      const token = await auth.generateToken({ ...user.dataValues })


      responseMessage({
        res,
        data: { token },

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
   * @function update
   * @description updates a user
   * @param {object} username - user's username
   * @param {object} role - user's role
   * @param {object} profile - user's profile
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  updateUser = async (req, res, next) => {
    try {

      const user = await this.service.findOne({ ...req.params });

      if (
        !user ||
        (req.user.role !== "superAdmin" &&
          user.dataValues.role === "admin" &&
          user.dataValues.id !== req.user.id)
      ) {
        throw global.error(400, messages.m.USER_NOT_FOUND);

      const user = await this.service.findOne({ ...req.params })

      if (
        !user ||
        (req.user.role !== 'superAdmin' &&
          user.dataValues.role === 'admin' &&
          user.dataValues.id !== req.user.id)
      ) {
        throw global.error(400, messages.m.USER_NOT_FOUND)

      }

      const updatedDocument = await this.service.updateOne(
        {
          ...req.body,
        },
        {
          ...req.params,
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
   * @function getUser
   * @description find a specific row in a table
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  getUser = async (req, res, next) => {
    try {

      const id = req.params.id;

      const clause = {
        id,
      };

      const apiKey = await this.service.findUserApiKey(clause);
      responseMessage({
        res,
        data: apiKey,
      });
    } catch (error) {
      next(error);
    }
  };

      let id = req.params.id

      let clause = {
        id,
      }

      const apiKey = await this.service.findUserApiKey(clause)
      responseMessage({
        res,
        data: apiKey,
      })
    } catch (error) {
      next(error)
    }
  }


  /**
   * @function getAllUsers
   * @description find a specific row in a table
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  getAllUsers = async (req, res, next) => {
    try {

      const role = req.user.role === "superAdmin" ? null : "user";

      const clause = {
        role,
      };

      const apiKey = await this.service.findAllUsersApiKey(clause);
      responseMessage({
        res,
        data: apiKey,
      });
    } catch (error) {
      next(error);
    }
  };

      const role = req.user.role === 'superAdmin' ? null : 'user'

      const clause = {
        role,
      }

      const apiKey = await this.service.findAllUsersApiKey(clause)
      responseMessage({
        res,
        data: apiKey,
      })
    } catch (error) {
      next(error)
    }
  }


  /**
   * @function removeUser
   * @description remove a specific row in a table
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  removeUser = async (req, res, next) => {
    try {

      const user = await this.service.findOne({ ...req.params });

      if (
        !user ||
        (req.user.role === "admin" && user.dataValues.role === "superAdmin") ||
        (req.user.role === "admin" && user.dataValues.id !== req.user.id)
      ) {
        throw global.error(400, messages.m.USER_NOT_FOUND);

      const user = await this.service.findOne({ ...req.params })

      if (
        !user ||
        (req.user.role === 'admin' && user.dataValues.role === 'superAdmin') ||
        (req.user.role === 'admin' && user.dataValues.id !== req.user.id)
      ) {
        throw global.error(400, messages.m.USER_NOT_FOUND)

      }

      const deletedDocument = await this.service.deleteOne({
        where: { ...req.params },

      });

      })


      responseMessage({
        res,
        data: deletedDocument,

      });
    } catch (error) {
      next(error);
    }
  };
})(service);

      })
    } catch (error) {
      next(error)
    }
  }
})(service)

