
```javascript



import pkg from "bcryptjs";
const { hash, compare, genSaltSync } = pkg;
/**
 * representive class of bcrypt package methods
 * @class
 */
export default class {
  /**
   * @function createHashPass
   * @description creates a hashed password
   * @param {string} password - user password to hash
   * @return {string} hashed password string
   */
  static createHashPass = (password) => {
    let output;
    hash(password, genSaltSync(12)).then((res) => {
      this.output = res;
    });
    return output;
  };

  /**
   * @function compareHashPass
   * @description compares a hashed password with it's original password
   * @param {string} passwordFromUser - password sent in request body
   * @param {string} passwordFromDb - user password in DB
   * @return {boolean} returns true if hashed password is equal to original password
   */
  static compareHashPass = (passwordFromUser, passwordFromDb) => {
    let output;
    compare(passwordFromUser, passwordFromDb).then((res) => {
      this.output = res;
    });
    return output;
  };
}
```