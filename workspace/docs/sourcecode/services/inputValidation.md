
```javascript



import fs from "fs";
import { exec } from "child_process";
import { promisify } from "util";
const execFunc = promisify(exec);
/**
 * Class representing file validation
 * @class
 */
export default new (class {
  /**
   * @function characterCounter
   * @description declares text's character count
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  characterCounter = (text) => {
    try {
      const textLength = text.split("").length;

      return textLength;
    } catch (error) {
      console.log(error);
    }
  };

  /**
   * @function FileSize
   * @description find a specific row in a table
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  FileSize = async (req, res, next) => {
    try {
      const modifier = Object.keys(req.files)[0];
      const { path } = req.files[modifier][0];
      const stats = fs.statSync(path);
      const fileSizeInBytes = stats.size;

      const fileSize = this.convertSize(fileSizeInBytes);
      return fileSize;
    } catch (error) {
      next(error);
    }
  };

  /**
   * @function fileDuration
   * @description declare duration of a file based on input path
   * @param {object} req - request object
   * @param {object} res - response object
   * @param {function} next - function that send request to the next middleware
   */
  // fileDuration = async (path) => await getVideoDurationInSeconds(path)
  fileDuration = async (filePath) => {
    const { stdout } = await execFunc(
      `ffprobe -i ${filePath} -show_entries format=duration -v quiet -of csv='p=0'`
    );
    return parseFloat(stdout);
  };

  convertTime = (duration) => {
    let time = null;
    if (duration >= 60) {
      const minutes = Math.floor(duration / 60);
      const seconds = (duration - minutes * 60).toFixed(0);

      time = `${minutes}m:${seconds}s`;
    } else {
      const seconds = duration.toFixed(0);
      const milSec = duration.toString().split(".")[1].split("")[0];

      time = `${seconds}s:${milSec}ms`;
    }
    return time;
  };

  convertSize = (fileSizeInBytes) => {
    let fileSizeInMegabytes = null;
    fileSizeInBytes = fileSizeInBytes / 1000.0;

    if (fileSizeInBytes >= 1000 && fileSizeInBytes <= 1000000) {
      fileSizeInMegabytes = (fileSizeInBytes / 1000.0).toFixed(2) + " mB";
    } else {
      fileSizeInMegabytes = fileSizeInBytes + "kB";
    }
    return fileSizeInMegabytes;
  };
})();
```