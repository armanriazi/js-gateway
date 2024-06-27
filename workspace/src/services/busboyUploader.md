
```javascript



import path, { extname } from "path";
import { randomUUID } from "crypto";
import fs from "fs";
import busboy from "busboy";
import messages from "../common/messages.js";

/**
 * Class representing upload services
 */
export default class Uploader {
  /**
  Handles file upload requests
  * @param {Request} req - The HTTP request object
  * @param {Response} res - The HTTP response object
  * @param {NextFunction} next - The next middleware function
  * @returns {void}
*/
  static upload = (req, res, next) => {
    try {
      if (req.requestType === "proxy" && !req.srtProxy) {
        return next();
      }
      this.counter = 0;
      this.hasFile = false;
      this.req = req;
      this.next = next;

      this.fileRecived = [];
      this.listFiles = [];

      const aiServiceFiles = this.req.accessAiService.files;

      this.aiServiceFile = aiServiceFiles;

      this.req.filePath = this.req.filePath || {};

      const contentTypeError = this.contentTypeHandler();
      if (contentTypeError) throw contentTypeError;

      this.getListFile(aiServiceFiles);

      if (
        !this.req.get("content-type").includes("multipart/form-data") &&
        this.listFiles.length !== 0
      )
        throw global.error(400, messages.m.FILE_MUST_MULTI);

      if (!this.req.get("content-type").includes("multipart/form-data"))
        return this.next();

      const bb = busboy({ headers: this.req.headers });
      this.bb = bb;
      this.req.files = [];
      this.req.file = null;

      this.bb.on("field", this.bodyHandler.bind(this));
      this.bb.on("file", (fieldname, file, filename) => {
        const matchingFile = aiServiceFiles.find((fileObj) => {
          return fileObj.name === fieldname;
        });

        if (matchingFile) {
          this.writeFile(matchingFile, file, filename);
        } else {
          // Invalid field name, emit an error
          const error = {
            status: 400,
            message: messages.m.INVALID_FILE_ENTRY,
          };
          this.bb.emit("error", error);
        }
      });
      this.bb.on("close", () => {
        if (this.counter === 0) {
          this.finishProcess();
        }
      });
      req.pipe(this.bb);
      req = this.req;
      this.bb.on("error", this.errorHandlingEvent.bind(this));
    } catch (error) {
      console.log(error);
      next(error);
    }
  };

  /**
   * push files to array listFiles
   * @type {function}
   * @function getListFile
   */
  static getListFile = (aiServiceFiles) => {
    for (const aiServiceFile of aiServiceFiles) {
      if (aiServiceFile.howGet === "file") {
        this.listFiles.push(aiServiceFile);
      }
    }
  };

  /**
   * check type file is incorrect
   * @type {function}
   * @function checkValidExtension
   */
  static checkValidExtension = ({ serviceFileType, extension }) => {
    const imageExtension = [".jpg", ".jpeg", ".png", ".bmp"];
    const videoExtension = [
      ".mp4",
      ".mkv",
      ".mov",
      ".avi",
      ".mpeg",
      ".wmv",
      ".webm",
    ];
    const voiceExtension = [".mp3", ".mp4", ".wav", ".ogg", ".flac"];
    switch (serviceFileType) {
      case "image":
        if (!imageExtension.includes(extension)) {
          this.bb.emit(
            "error",
            global.error(400, messages.m.INVALID_EXTEN_IMAGE)
          );
          return { error: true };
        }
        break;

      case "video":
        if (!videoExtension.includes(extension)) {
          this.bb.emit("error", {
            status: 400,
            message: messages.m.INVALID_EXTEN_VIDEO,
          });
          return { error: true };
        }
        break;

      case "voice":
        if (!voiceExtension.includes(extension)) {
          this.bb.emit("error", {
            status: 400,
            message: messages.m.INVALID_EXTEN_VOICE,
          });
          return { error: true };
        }
        break;

      default:
        this.bb.emit("error", {
          status: 400,
          message: messages.m.INCORRECT_INFO_SERVICE,
        });
        return { error: true };
    }
    return { error: false };
  };

  /**
   * A static method that checks if a 'Content-Type' header is present in the request.
   * If the header is not present, it throws a 400 Bad Request error.
   * @returns {null} Returns null if the 'Content-Type' header is present.
   * @throws {Error} Throws a 400 Bad Request error if the 'Content-Type' header is not present.
   */
  static contentTypeHandler() {
    if (!this.req.headers["content-type"]) {
      throw global.error(400, messages.m.FILE_NOT_SENT);
    }
    return null;
  }

  /**
   * A static method that handles the body of the request and adds it to the 'req.body' object.
   * @param {string} name - The name of the field in the request body.
   * @param {string} value - The value of the field in the request body.
   * @returns {void} Returns nothing.
   */
  static bodyHandler(name, value) {
    try {
      if (name in this.req.body) {
        if (Array.isArray(this.req.body[name])) {
          this.req.body[name].push(value);
        } else {
          this.req.body[name] = [this.req.body[name], value];
        }
      } else {
        this.req.body[name] = JSON.parse(value);
      }
    } catch (error) {
      this.req.body[name] = value;
    }
  }

  /**
   * A static method that writes a file to disk.
   * @param {string} name - The name of the file.
   * @param {Object} file - A ReadStream object representing the file to be written to disk.
   * @param {Object} info - An object containing information about the file (e.g. filename, mimeType, etc.).
   * @returns {Object|undefined} Returns an object with an 'error' property set to true if there was an error, or undefined if the operation completed successfully.
   */
  static writeFile(matchingFile, file, info) {
    const { name, type } = matchingFile;

    this.hasFile = true;
    this.counter++;

    const { filename, encoding, mimeType } = info;

    const extension = extname(filename);
    const { error } = this.checkValidExtension({
      serviceFileType: type,
      extension,
    });
    if (error) return;
    const newName = `gateway-file-${randomUUID()}${extname(filename)}`;
    const saveTo = path.join(process.cwd(), "assets/uploads", newName);

    const writeStream = fs.createWriteStream(saveTo);
    file.pipe(writeStream);
    let size = 0;
    file.on("data", function (data) {
      size += Number(data.length);
    });

    writeStream.on("close", () => {
      this.fileRecived.push(name);

      this.req.filePath[name] = saveTo;

      this.req.files.push({
        originalname: filename,
        name: newName,
        path: saveTo,
        encoding,
        mimeType,
        size,
        extension: path.extname(filename),
      });
      this.counter--;

      if (this.counter === 0) {
        this.finishProcess();
      }
    });

    writeStream.on("error", (error) => {
      this.next(error);
    });
    // } else {
    //   const error = {
    //     status: 400,
    //     message: messages.m.INVALID_FILE_ENTRY,
    //   }
    //   this.bb.emit('error', error)
    // }
    // }
  }

  /**
   * A static method that handles errors that occur during the file upload process.
   * @param {Error} error - The error that occurred during the file upload process.
   * @returns {void} Returns nothing.
   */
  static errorHandlingEvent(error) {
    this.req.unpipe(this.bb);
    this.bb.removeAllListeners();
    this.req.on("readable", this.req.read.bind(this.req));
    this.next(error);
  }

  /**
   * A static method that finishes the file upload process.
   * @returns {Object|undefined} Returns an object with an 'error' property set to true if there was an error, or undefined if the operation completed successfully.
   * @throws {Error} Throws an error if a required file was not received.
   */
  static finishProcess() {
    for (const file of this.listFiles) {
      if (!this.fileRecived.includes(file.name) && file.required) {
        const error = { status: 400, message: `الزامی است ${file.name}` };
        this.bb.emit("error", error);

        return { error: true };
      }
    }
    this.next();
  }
}
```