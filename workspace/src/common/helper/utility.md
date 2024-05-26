
```javascript



import { Op } from "sequelize";

/**
 * representive class of bcrypt package methods
 * @class
 */
export default class {
  /**
   * api درخواست زدن به یک
   * @function filterQueryBuilder
   * @description با توجه به کانفیگ ورودی یک درخواست میزند اگر ادرسی وجود داشت مقدار را با توجه به ادرس می فرستد
   * @param {object} axiosConfig - کانفیگ
   * @param {string} address - ادرس
   */
  static filterQueryBuilder = ({ startTimeStamp, endTimeStamp }) => {
    try {
      let where = {
        createdAt: {},
      };

      if (startTimeStamp) {
        where.createdAt[Op.gte] = startTimeStamp;
      }
      if (endTimeStamp) {
        where.createdAt[Op.lte] = endTimeStamp;
      } else if (!startTimeStamp && !endTimeStamp) {
        where = null;
      }

      return where;
    } catch (error) {
      return null;
    }
  };
}
```