```javascript
import jwt from "../common/helper/jwt.js";
import redis from "../config/redis.js";
import env from "../common/env.js";
import userService from "../modules/users/users.service.js";
import messages from "../common/messages.js";

/**
 * representive class of Authentication
 * @class
 */
export default new (class Auth {
  /**
   * @function check
   * @description verifies token and check user's authorization
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  check = async (req, res, next) => {
    try {
      let token = req.get("Authorization");

      if (!token) throw global.error(401, messages.m.LOGIN_NEEDED);

      token = token.replace("Bearer ", "");

      const payload = await jwt.verifyToken(token);

      if (!payload) throw global.error(403, messages.m.ACCESS_DENIED);

      const user = await userService.findOne({ id: payload.id });

      if (!user) throw global.error(400, messages.m.LOGIN_NEEDED);

      const { id, username, role } = user.dataValues;

      await this.checkSession(id);

      req.user = { id, username, role };

      next();
    } catch (error) {
      next(error);
    }
  };

  /**
   * @function verifyToken
   * @description verifies token
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  verifyToken = async (token) => {
    try {
      const payload = await jwt.verifyToken(token);

      return payload;
    } catch (error) {
      console.log(error);
    }
  };

  /**
   * @function generateToken
   * @description generates token and sets user session
   * @param {object}
   */
  async generateToken({ id, role }) {
    const token = jwt.createToken({ id, role });
    const redisKey = `${id}_token`;

    await redis.setex(redisKey, Number(env("SESSION_EXPIRATION")), token);

    return token;
  }

  /**
   * @function checkSession
   * @description checks validation period of a token
   * @param {object}
   */
  async checkSession(id) {
    const exist = await redis.exists(`${id}_token`);

    if (!exist) throw global.error(401, messages.m.SESSION_OVER);
  }

  /**
   * @function checkIdInParamsWithReqUserId req.user to user id in params
   * @description user can only access to its own resources
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  checkIdInParamsWithReqUserId = async (req, res, next) => {
    try {
      const targetUserId = req.params.id;
      const { role } = req.user;

      if (role === "user" && targetUserId !== req.user.id) {
        throw global.error(403, messages.m.ACCESS_DENIED);
      }

      next();
    } catch (error) {
      next(error);
    }
  };

  /**
   * @function simpleCheckRoles req.user to user id in params
   * @description checks if userRole is authorized to specified roles
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  simpleCheckRoles = (roles) => {
    return (req, res, next) => {
      try {
        if (!roles.includes(req.user.role))
          throw global.error(403, messages.m.ACCESS_DENIED);

        next();
      } catch (err) {
        next(err);
      }
    };
  };

  /**
   * @function checkRequestRole req.user to user id in params
   * @description denies admin from trying to submit any roleName but user
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  checkRequestRole = (req, res, next) => {
    const role = req.user.role;
    try {
      if (role === "admin" && req.body.role !== "user") {
        throw global.error(403, messages.m.NOT_ALLOW_ROLE);
      }
      next();
    } catch (err) {
      next(err);
    }
  };

  /**
   * @function checkAdminIsAuthorized req.user to user id in params
   * @description this method checks if admin is authorized to do smth
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  checkAdminIsAuthorized = async (req, res, next) => {
    try {
      const id = req.body.id;
      const doc = await userService.findUserRole(id);

      if (doc[0].dataValues.id === req.user.id || req.body.role === "user")
        throw global.error(403, messages.m.NOT_ALLOW_ROLE);

      next();
    } catch (err) {
      next(err);
    }
  };

  /**
   * @function checkAdminCanCreateApiKey req.user to user id in params
   * @description this method checks if admin is authorized to do create apiKey or not
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  checkAdminCanCreateApiKey = async (req, res, next) => {
    try {
      const id = req.body.UserId;
      const doc = await userService.findOne({ id });
      const role = req.user.role;

      if (role === "superAdmin") {
        next();
      } else if (
        doc.dataValues.role === "user" ||
        req.user.id === doc.dataValues.id
      ) {
        next();
      } else {
        throw global.error(403, messages.m.ACCESS_DENIED);
      }
    } catch (err) {
      next(err);
    }
  };

  /**
   * @function checkAdminCanNotGetAdmins req.user to user id in params
   * @description this method checks if admin is authorized to do create apiKey or not
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  checkAdminCanNotGetAdmins = async (req, res, next) => {
    try {
      const id = req.params.id;
      const doc = await userService.findOne({ id });
      const role = req.user.role;

      if (role === "superAdmin") {
        next();
      } else if (
        doc.dataValues.role === "user" ||
        req.user.id === doc.dataValues.id
      ) {
        next();
      } else {
        throw global.error(403, messages.m.USER_NOT_FOUND);
      }
    } catch (err) {
      next(err);
    }
  };

  /**
   * @function checkRoleIsAllowed req.user to user id in params
   * @description verifies token and checks if role is allowed to do an action
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  checkRoleIsAllowed = (field) => {
    return async (req, res, next) => {
      try {
        if (req.user.role !== "superAdmin" && req.body[field]) {
          throw global.error(403, messages.m.NOT_ALLOW_ROLE);
        }

        next();
      } catch (error) {
        next(error);
      }
    };
  };
})();
```