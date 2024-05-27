
```javascript



const axios = require("axios");
// const https = require('https')

/**
 * representive class of bcrypt package methods
 * @class
 */
export default class Auth {
  /**
   * api درخواست زدن به یک
   * @function sendRequest
   * @description با توجه به کانفیگ ورودی یک درخواست میزند اگر ادرسی وجود داشت مقدار را با توجه به ادرس می فرستد
   * @param {object} axiosConfig - کانفیگ
   * @param {string} address - ادرس
   */
  static sendRequest = async (axiosConfig, address) => {
    try {
      let result = await axios(axiosConfig);

      if (address) {
        address = address.split(".");

        for (let index = 0; index < address.length; index++) {
          result = result[address[index]];
        }
      }
      return result;
    } catch (error) {
      return null;
    }
  };
}
```