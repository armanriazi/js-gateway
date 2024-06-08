
```javascript



import crypto from "crypto";
import path from "path";
import multer from "multer";
import messages from "../common/messages.js";

/**
 * Class representing Multer Uploader
 * @class
 */
export default new (class {
  constructor() {
    this.multer = multer({
      storage: multer.diskStorage({
        destination: (req, file, cb) => {
          cb(null, `${process.cwd()}/uploads`);
        },
        filename: (req, file, cb) => {
          cb(null, `${crypto.randomUUID()}${path.extname(file.originalname)}`);
        },
      }),
      fileFilter: function (req, file, cb) {
        switch (file.fieldname) {
          case "selfie":
            if (
              ![".jpg", ".jpeg", ".png", ".bmp"].includes(
                path.extname(file.originalname)
              )
            ) {
              return cb(
                new Error(
                  `${messages.c.SELFIE} ${messages.m.NOT_ENTERED_CORRECTLY}`
                )
              );
            } else {
              return cb(null, true);
            }

          case "video":
            if (
              ![
                ".mp4",
                ".mkv",
                ".mov",
                ".avi",
                ".mpeg",
                ".wmv",
                ".webm",
              ].includes(path.extname(file.originalname))
            ) {
              return cb(
                new Error(
                  `${messages.c.VIDEO} ${messages.m.NOT_ENTERED_CORRECTLY}`
                )
              );
            } else {
              return cb(null, true);
            }

          case "file":
            if (
              ![".jpg", ".jpeg", ".png", ".bmp", ".mp3"].includes(
                path.extname(file.originalname)
              )
            ) {
              return cb(
                new Error(
                  `${messages.c.FILE} ${messages.m.NOT_ENTERED_CORRECTLY}`
                )
              );
            } else {
              return cb(null, true);
            }
        }
      },
    }).fields([
      { name: "selfie", maxCount: 1 },
      { name: "video", maxCount: 1 },
      { name: "file", maxCount: 10 },
    ]);
  }

  /**
   * @function uploader
   * @description uploads form-data input files on server disk
   * @param {object} req - express request object
   * @param {object} res - express response object
   * @param {function} next - function that sends request to the next middleware
   */
  uploader = (req, res, next) => {
    this.multer(req, res, (err) => {
      try {
        if (err) {
          if (err.message === "Unexpected field") {
            throw global.error(400, messages.m.UNEXPECTED_FIELD);
          }

          if (err.message === messages.m.VALIDATETYPEOFFILEERROR) {
            throw global.error(400, messages.m.VALIDATETYPEOFFILEERROR);
          }

          throw global.error(400, messages.m.FILEUPLOADERROR);
        }
        next();
      } catch (err) {
        next(err);
      }
    });
  };
})();
```