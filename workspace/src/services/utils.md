
```javascript



/* eslint-disable quotes */
import redis from "../config/redis.js";
import { existsSync, unlinkSync } from "fs";

/**
 * representive class of declaring expireDate for apiKey
 * @class
 */
export default class {
  /**
   * @function afterTime
   * @description creates a time
   * @param {string} clause - declaring which time user wants to set for expireDate of apiKey
   * @return {string} expireDate
   */
  static afterTime = (clause) => {
    const letterPatern = /[a-zA-Z]/g;
    const letter = clause.match(letterPatern);
    const number = clause.replace(letter[0], "");
    const condition = letter[0] === "d" ? 60 * 24 * number : 60 * number;
    const currentTime = new Date();
    const dateString = new Date(
      currentTime.getTime() + condition * 60000
    ).toLocaleString("en-US");
    const timeStamp = new Date(dateString);
    const year = timeStamp.getFullYear();
    const month = ("0" + (timeStamp.getMonth() + 1)).slice(-2);
    const date = ("0" + timeStamp.getDate()).slice(-2);
    const hours = ("0" + timeStamp.getHours()).slice(-2);
    const minutes = ("0" + timeStamp.getMinutes()).slice(-2);
    const seconds = ("0" + timeStamp.getSeconds()).slice(-2);
    const dateTime = `${year}-${month}-${date} ${hours}:${minutes}:${seconds}`;

    return dateTime;
  };

  /**
   * @function redisSetKey
   * @description generates token and sets user session
   * @param {object}
   */
  static async redisSetKey({ key, seconds, value }) {
    const token = await redis.setex(key, seconds, value);
    return token;
  }

  /**
   * حذف فایل
   * @function deleteFile
   * @param {file} files
   * @return {null}
   */
  static deleteFile = (files = []) => {
    for (const file of files) {
      existsSync(file.path) && unlinkSync(file.path);
    }
  };

  // static addNestedImagePath = (entireObj, originalUrl) => {

  //   const url = originalUrl.split('/').slice(0, -1).join('/')

  //   JSON.stringify(entireObj, (_, nestedValue) => {
  //     if (nestedValue && nestedValue['imagePath']) {
  //       nestedValue.imagePath = url + '/' + nestedValue.imagePath
  //     } else if (nestedValue && nestedValue['filePath']) {
  //       nestedValue.filePath = url + '/' + nestedValue.filePath
  //     }

  //     return nestedValue
  //   })

  //   return entireObj
  // }

  static addNestedImagePath = (entireObj, originalUrl) => {
    const url = originalUrl.split("/").slice(0, -1).join("/");

    const updatePaths = (obj) => {
      for (const key in obj) {
        if (typeof obj[key] === "object") {
          updatePaths(obj[key]);
        } else if (key === "imagePath" || key === "filePath") {
          obj[key] = `${url}/${obj[key]}`;
        }
      }
    };

    updatePaths(entireObj);

    return entireObj;
  };

  static stringToBoolean = (obj) => {
    for (const key in obj) {
      const clause =
        typeof obj[key] === "string" ? obj[key].toLowerCase().trim() : obj[key];

      switch (clause) {
        case "true":
        case "1":
        case true:
        case 1:
        case '"1"':
        case "'true'":
        case "'1'":
        case '"true"':
          obj[key] = true;
          break;

        case "false":
        case "0":
        case false:
        case 0:
        case '"0"':
        case "'false'":
        case "'0'":
        case '"false"':
          obj[key] = false;
          break;
      }
    }

    return obj;
  };

  /**
   * @summary متد بررسی فلگ های true
   * @function
   * @param {object} req
   */
  static filterValues = (req) => {
    const trueValueTypes = ["true", "1", true, 1, "True"];
    const falseValueTypes = ["false", "0", false, 0, "False"];

    for (const value in req.body) {
      if (trueValueTypes.includes(req.body[value])) {
        req.body[value] = true;
      } else if (falseValueTypes.includes(req.body[value])) {
        req.body[value] = false;
      }
    }
  };
}
```