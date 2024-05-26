
```javascript



import ajvInstance from "../../services/ajvInstance.js";

/**
 * representive class of ajv Validator
 * @class
 */
export default class {
  /**
   * check api is valid in method GET if is not valid return not found or access denied
   * @type {function}
   * @function ajvValidator
   * @param {object} req - express request object
   * @param {object} res - express response object
   * @param {function} next - function that sends request to the next middleware
   */
  static ajvValidator = (req, res, next) => {
    try {
      if (req.requestType === "proxy" && !req.srtProxy) {
        return next();
      }
      const { accessAiService } = req;
      const ajvValidate = ajvInstance.compile(
        accessAiService.Service.validation
      );
      const { body } = req;
      const aiServiceFiles = req.accessAiService.files;
      const inputs = this.removeFileFromInput({
        inputs: { ...body },
        aiServiceFiles,
      });
      const validate = ajvValidate(inputs);

      if (!validate) {
        const { errors } = ajvValidate;
        throw global.error(400, errors[0]);
      }

      const schemaProperties =
        ajvValidate.schema && ajvValidate.schema.properties;
      const properties = schemaProperties ? Object.keys(schemaProperties) : [];

      if (properties.length < 1) {
        this.fillInputObject(body, properties);
      }

      next();
    } catch (err) {
      console.log(err);
      next(err);
    }
  };

  /**
   * fill up input flags which are not written
   * @type {function}
   * @function fillInputObject
   */
  static fillInputObject = (body, properties) => {
    for (const field of properties) {
      if (!body[field]) {
        body[field] = false;
      }
    }
    return body;
  };

  /**
   * remove base64 and link from req body
   * @type {function}
   * @function removeFileFromInput
   */
  static removeFileFromInput = ({ inputs, aiServiceFiles }) => {
    for (const file of aiServiceFiles) {
      if (file.howGet === "base64" || file.howGet === "link") {
        delete inputs[file.howGet];
      }
    }

    return inputs;
  };
}
```