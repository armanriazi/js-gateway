
```javascript



import { extname } from "path";
import inputValidation from "../../services/inputValidation.js";
import messages from "../messages.js";

export default class {
  /**
   * check policy such image size, video size, count character
   * @type {function}
   * @param {object} req - express request object
   * @param {object} res - express response object
   * @param {function} next - function that sends request to the next middleware
   */
  static policy = async (req, res, next) => {
    try {
      if (req.requestType === "proxy") {
        return next();
      }
      const { policies } = req.accessAiService;
      const { files } = req;
      const entryNlp = [
        "document",
        "entry",
        "sentence",
        "data",
        "text",
        "input_text",
        "query",
        "content",
      ];
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
      const voiceExtension = [".mp3"];

      for (const policyKey in policies) {
        if (policyKey === "image") {
          await this.checkPolicy({
            extension: imageExtension,
            files,
            policy: policies[policyKey],
          });
        }

        if (policyKey === "video") {
          await this.checkPolicy({
            extension: videoExtension,
            files,
            policy: policies[policyKey],
          });
        }

        if (policyKey === "voice") {
          await this.checkPolicy({
            extension: voiceExtension,
            files,
            policy: policies[policyKey],
          });
        }

        if (policyKey === "character") {
          const input = { ...req.body };
          const textKey = Object.keys(input).find((inputKey) =>
            entryNlp.includes(inputKey)
          );

          if (textKey) {
            const text = input[textKey];
            const textLength = inputValidation.characterCounter(text);
            const characterCounterPolicy = policies[policyKey].counter;

            if (textLength > characterCounterPolicy)
              throw global.error(403, messages.m.INVALID_COUNT_CHARACTER);
          }
        }
      }

      next();
    } catch (err) {
      next(err);
    }
  };

  /**
   * check file size and duration
   * @type {function}
   */
  static checkPolicy = async ({ extension, files, policy }) => {
    for (const file of files) {
      if (extension.includes(extname(file.originalname))) {
        for (const [policyKey, policyValue] of Object.entries(policy)) {
          if (policyKey === "size") {
            if (file.size > policyValue)
              throw global.error(403, messages.m.INVALID_FILE_SIZE);
          }

          if (policyKey === "duration") {
            const duration = await inputValidation.fileDuration(file.path);
            if (duration > policyValue)
              throw global.error(403, messages.m.INVALID_FILE_DURATION);
          }
        }
      }
    }
    return true;
  };
}
```