
import Joi from "joi";
import messages from "../../common/messages.js";

import Joi from 'joi'
import messages from '../../common/messages'


export default {
  create: Joi.object({
    name: Joi.string()
      .required()
      .messages({

        "string.base": `${messages.c.PROJECT_NAME} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.PROJECT_NAME} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.PROJECT_NAME} ${messages.m.REQUIRED}`,

        'string.base': `${messages.c.PROJECT_NAME} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.PROJECT_NAME} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.PROJECT_NAME} ${messages.m.REQUIRED}`,

      }),
  }),

  index: Joi.object({
    offs: Joi.number()
      .integer()
      .messages({

        "number.base": `${messages.c.PAGE} ${messages.m.INCORRECT}`,
        "number.integer": `${messages.c.PAGE} ${messages.m.PATTERN}`,
        "number.empty": `${messages.c.PAGE} ${messages.m.EMPTY}`,

        'number.base': `${messages.c.PAGE} ${messages.m.INCORRECT}`,
        'number.integer': `${messages.c.PAGE} ${messages.m.PATTERN}`,
        'number.empty': `${messages.c.PAGE} ${messages.m.EMPTY}`,

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

        'number.base': `${messages.c.LIMIT} ${messages.m.INCORRECT}`,
        'number.integer': `${messages.c.LIMIT} ${messages.m.PATTERN}`,
        'number.empty': `${messages.c.LIMIT} ${messages.m.EMPTY}`,
      }),
    startTimeStamp: Joi.date().messages({
      'date.base': `${messages.c.START_TIMESTAMP} ${messages.m.INCORRECT}`,
      'date.empty': `${messages.c.START_TIMESTAMP} ${messages.m.EMPTY}`,
    }),
    endTimeStamp: Joi.date().messages({
      'date.base': `${messages.c.END_TIMESTAMP} ${messages.m.INCORRECT}`,
      'date.empty': `${messages.c.END_TIMESTAMP} ${messages.m.EMPTY}`,

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

        'string.base': `${messages.c.ID} ${messages.m.INCORRECT}`,
        'string.guid': `${messages.c.ID} ${messages.m.PATTERN}`,
        'string.empty': `${messages.c.ID} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.ID} ${messages.m.REQUIRED}`,

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

        'string.base': `${messages.c.ID} ${messages.m.INCORRECT}`,
        'string.guid': `${messages.c.ID} ${messages.m.PATTERN}`,
        'string.empty': `${messages.c.ID} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.ID} ${messages.m.REQUIRED}`,

      }),
    name: Joi.string()
      .required()
      .messages({

        "string.base": `${messages.c.PROJECT_NAME} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.PROJECT_NAME} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.PROJECT_NAME} ${messages.m.REQUIRED}`,

        'string.base': `${messages.c.PROJECT_NAME} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.PROJECT_NAME} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.PROJECT_NAME} ${messages.m.REQUIRED}`,

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
};

        'string.base': `${messages.c.ID} ${messages.m.INCORRECT}`,
        'string.guid': `${messages.c.ID} ${messages.m.PATTERN}`,
        'string.empty': `${messages.c.ID} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.ID} ${messages.m.REQUIRED}`,
      }),
  }),
}

