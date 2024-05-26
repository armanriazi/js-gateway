
```javascript



import Joi from "joi";
import messages from "../../common/messages.js";

export default {
  create: Joi.object({
    expireDate: Joi.string()
      .pattern(/^([0-9]+)([dhDH])$/)
      .required()
      .messages({
        "string.base": `${messages.c.EXPIRE_DATE} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.EXPIRE_DATE} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.EXPIRE_DATE} ${messages.m.REQUIRED}`,
        "string.pattern.base": messages.m.EXPIRE_DATE_FORMAT,
      }),
    UserId: Joi.string()
      .guid()
      .required()
      .messages({
        "string.base": `${messages.c.USER_ID} ${messages.m.INCORRECT}`,
        "string.guid": `${messages.c.USER_ID} ${messages.m.PATTERN}`,
        "string.empty": `${messages.c.USER_ID} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.USER_ID} ${messages.m.REQUIRED}`,
      }),
    maxCount: Joi.number()
      .integer()
      .messages({
        "number.base": `${messages.c.MAX_COUNT} ${messages.m.INCORRECT}`,
        "number.integer": `${messages.c.MAX_COUNT} ${messages.m.PATTERN}`,
      }),
  }),

  index: Joi.object({
    offs: Joi.number()
      .integer()
      .messages({
        "number.base": `${messages.c.PAGE} ${messages.m.INCORRECT}`,
        "number.integer": `${messages.c.PAGE} ${messages.m.PATTERN}`,
        "number.empty": `${messages.c.PAGE} ${messages.m.EMPTY}`,
      }),
    limit: Joi.number()
      .integer()
      .messages({
        "number.base": `${messages.c.LIMIT} ${messages.m.INCORRECT}`,
        "number.integer": `${messages.c.LIMIT} ${messages.m.PATTERN}`,
        "number.empty": `${messages.c.LIMIT} ${messages.m.EMPTY}`,
      }),
    startTimeStamp: Joi.date().messages({
      "date.base": `${messages.c.START_TIMESTAMP} ${messages.m.INCORRECT}`,
      "date.empty": `${messages.c.START_TIMESTAMP} ${messages.m.EMPTY}`,
    }),
    endTimeStamp: Joi.date().messages({
      "date.base": `${messages.c.END_TIMESTAMP} ${messages.m.INCORRECT}`,
      "date.empty": `${messages.c.END_TIMESTAMP} ${messages.m.EMPTY}`,
    }),
  }),

  get: Joi.object({
    id: Joi.string()
      .guid()
      .required()
      .messages({
        "string.base": `${messages.c.ID} ${messages.m.INCORRECT}`,
        "string.guid": `${messages.c.ID} ${messages.m.PATTERN}`,
        "string.empty": `${messages.c.ID} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.ID} ${messages.m.REQUIRED}`,
      }),
  }),

  remove: Joi.object({
    id: Joi.string()
      .guid()
      .required()
      .messages({
        "string.base": `${messages.c.ID} ${messages.m.INCORRECT}`,
        "string.guid": `${messages.c.ID} ${messages.m.PATTERN}`,
        "string.empty": `${messages.c.ID} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.ID} ${messages.m.REQUIRED}`,
      }),
  }),

  update: Joi.object({
    id: Joi.string()
      .guid()
      .required()
      .messages({
        "string.base": `${messages.c.ID} ${messages.m.INCORRECT}`,
        "string.guid": `${messages.c.ID} ${messages.m.PATTERN}`,
        "string.empty": `${messages.c.ID} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.ID} ${messages.m.REQUIRED}`,
      }),
    expireDate: Joi.string()
      .pattern(/^([0-9]+)([dhDH])$/)
      .messages({
        "string.base": `${messages.c.EXPIRE_DATE} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.EXPIRE_DATE} ${messages.m.EMPTY}`,
        "string.pattern.base": messages.m.EXPIRE_DATE_FORMAT,
      }),
    isValid: Joi.boolean().messages({
      "boolean.base": `${messages.c.IS_VALID} ${messages.m.INCORRECT}`,
      "boolean.empty": `${messages.c.IS_VALID} ${messages.m.EMPTY}`,
    }),
    maxCount: Joi.number()
      .integer()
      .messages({
        "number.base": `${messages.c.MAX_COUNT} ${messages.m.INCORRECT}`,
        "number.integer": `${messages.c.MAX_COUNT} ${messages.m.PATTERN}`,
      }),
  }),
};
```