
```javascript



import env from "../env.js";
import messages from "../messages.js";
import pify from "pify";
import pkg from "jsonwebtoken";
const { sign, verify } = pkg;

/**
 * representive class of bcrypt package methods
 * @class
 */
export default class {
  /**
   * @function verifyToken
   * @description verifies if received token is valid or not
   * @param {string} token - received user token
   * @return {object} token payload
   */
  static async verifyToken(token) {
    try {
      const pVerify = pify(verify);
      return await pVerify(token, env("JWT_SECRET_KEY"));
    } catch (err) {
      throw global.error(401, messages.m.TOKEN_EXPIRED);
    }
  }

  /**
   * @function createToken
   * @description creates a token for logged in user
   * @param {string} id - logged in user id
   * @param {object} Role - logged in user role information object
   * @return {string} created token
   */
  static createToken({ id, Role }) {
    const pSign = pify(sign);
    return pSign({ id, Role }, env("JWT_SECRET_KEY"));
  }
}
```