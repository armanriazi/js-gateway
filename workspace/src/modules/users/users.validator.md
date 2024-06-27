
import Joi from "joi";
import messages from "../../common/messages.js";

import Joi from 'joi'
import messages from '../../common/messages'


export default {
  create: Joi.object({
    username: Joi.string()
      .min(5)
      .max(36)
      .required()
      .messages({

        "string.empty": `${messages.c.USERNAME} ${messages.m.EMPTY}`,
        "string.base": `${messages.c.USERNAME} ${messages.m.INCORRECT}`,
        "any.required": `${messages.c.USERNAME} ${messages.m.REQUIRED}`,
        "string.min": `${messages.c.USERNAME} ${messages.m.MINMAXLENGTH}`,
        "string.max": `${messages.c.USERNAME} ${messages.m.MINMAXLENGTH}`,

        'string.empty': `${messages.c.USERNAME} ${messages.m.EMPTY}`,
        'string.base': `${messages.c.USERNAME} ${messages.m.INCORRECT}`,
        'any.required': `${messages.c.USERNAME} ${messages.m.REQUIRED}`,
        'string.min': `${messages.c.USERNAME} ${messages.m.MINMAXLENGTH}`,
        'string.max': `${messages.c.USERNAME} ${messages.m.MINMAXLENGTH}`,

      }),
    password: Joi.string()
      .pattern(
        /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,}$/
      )
      .required()
      .messages({

        "string.base": `${messages.c.PASSWORD} ${messages.m.INCORRECT}`,
        "string.pattern.base": `${messages.c.PASSWORD} ${messages.m.PATTERN}`,
        "string.empty": `${messages.c.PASSWORD} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.PASSWORD} ${messages.m.REQUIRED}`,
      }),
    role: Joi.string()
      .valid("user", "admin")
      .required()
      .messages({
        "string.base": `${messages.c.ROLE} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.ROLE} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.ROLE} ${messages.m.REQUIRED}`,
        "any.only": 'role باید یکی از مقادیر  "admin", "user" باشد',
      }),
    profile: Joi.object({
      firstName: Joi.string().messages({
        "string.base": `${messages.c.FIRST_NAME} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.FIRST_NAME} ${messages.m.EMPTY}`,
      }),
      lastName: Joi.string().messages({
        "string.base": `${messages.c.LAST_NAME} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.LAST_NAME} ${messages.m.EMPTY}`,

        'string.base': `${messages.c.PASSWORD} ${messages.m.INCORRECT}`,
        'string.pattern.base': `${messages.c.PASSWORD} ${messages.m.PATTERN}`,
        'string.empty': `${messages.c.PASSWORD} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.PASSWORD} ${messages.m.REQUIRED}`,
      }),
    role: Joi.string()
      .valid('user', 'admin')
      .required()
      .messages({
        'string.base': `${messages.c.ROLE} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.ROLE} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.ROLE} ${messages.m.REQUIRED}`,
        'any.only': 'role باید یکی از مقادیر  "admin", "user" باشد',
      }),
    profile: Joi.object({
      firstName: Joi.string().messages({
        'string.base': `${messages.c.FIRST_NAME} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.FIRST_NAME} ${messages.m.EMPTY}`,
      }),
      lastName: Joi.string().messages({
        'string.base': `${messages.c.LAST_NAME} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.LAST_NAME} ${messages.m.EMPTY}`,

      }),
      email: Joi.string()
        .email()
        .messages({

          "string.base": `${messages.c.EMAIL} ${messages.m.INCORRECT}`,
          "string.email": `${messages.c.EMAIL} ${messages.m.PATTERN}`,
          "string.empty": `${messages.c.EMAIL} ${messages.m.EMPTY}`,

          'string.base': `${messages.c.EMAIL} ${messages.m.INCORRECT}`,
          'string.email': `${messages.c.EMAIL} ${messages.m.PATTERN}`,
          'string.empty': `${messages.c.EMAIL} ${messages.m.EMPTY}`,

        }),
      phoneNumber: Joi.string()
        .pattern(/^(0)(9)[0-9]{9}$/)
        .messages({

          "string.base": `${messages.c.PHONE_NUMBER} ${messages.m.INCORRECT}`,
          "string.pattern.base": `${messages.c.PHONE_NUMBER} ${messages.m.PATTERN}`,
          "string.empty": `${messages.c.PHONE_NUMBER} ${messages.m.EMPTY}`,
          "any.required": `${messages.c.PHONE_NUMBER} ${messages.m.REQUIRED}`,
        }),
    }).messages({
      "object.base": `${messages.c.PROFILE} ${messages.m.INCORRECT}`,

          'string.base': `${messages.c.PHONE_NUMBER} ${messages.m.INCORRECT}`,
          'string.pattern.base': `${messages.c.PHONE_NUMBER} ${messages.m.PATTERN}`,
          'string.empty': `${messages.c.PHONE_NUMBER} ${messages.m.EMPTY}`,
          'any.required': `${messages.c.PHONE_NUMBER} ${messages.m.REQUIRED}`,
        }),
    }).messages({
      'object.base': `${messages.c.PROFILE} ${messages.m.INCORRECT}`,

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

        "string.guid": `${messages.c.ID} ${messages.m.PATTERN}`,
        "string.empty": `${messages.c.ID} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.ID} ${messages.m.REQUIRED}`,

        'string.guid': `${messages.c.ID} ${messages.m.PATTERN}`,
        'string.empty': `${messages.c.ID} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.ID} ${messages.m.REQUIRED}`,

      }),
  }),

  updatePassword: Joi.object({
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
    currentPassword: Joi.string()
      .required()
      .messages({

        "string.base": `${messages.c.CURRENT_PASSWORD} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.CURRENT_PASSWORD} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.CURRENT_PASSWORD} ${messages.m.REQUIRED}`,

        'string.base': `${messages.c.CURRENT_PASSWORD} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.CURRENT_PASSWORD} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.CURRENT_PASSWORD} ${messages.m.REQUIRED}`,

      }),
    newPassword: Joi.string()
      .pattern(
        /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{8,}$/
      )
      .required()
      .messages({

        "string.base": `${messages.c.PASSWORD} ${messages.m.INCORRECT}`,
        "string.pattern.base": `${messages.c.PASSWORD} ${messages.m.PATTERN}`,
        "string.empty": `${messages.c.PASSWORD} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.PASSWORD} ${messages.m.REQUIRED}`,
      }),
    confirmNewPassword: Joi.string()
      .valid(Joi.ref("newPassword"))
      .required()
      .messages({
        "string.base": `${messages.c.CONFIRM_PASSWORD} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.CONFIRM_PASSWORD} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.CONFIRM_PASSWORD} ${messages.m.REQUIRED}`,
        "any.only": `${messages.m.INCORRECT_CONFIRM_PASSWORD}`,

        'string.base': `${messages.c.PASSWORD} ${messages.m.INCORRECT}`,
        'string.pattern.base': `${messages.c.PASSWORD} ${messages.m.PATTERN}`,
        'string.empty': `${messages.c.PASSWORD} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.PASSWORD} ${messages.m.REQUIRED}`,
      }),
    confirmNewPassword: Joi.string()
      .valid(Joi.ref('newPassword'))
      .required()
      .messages({
        'string.base': `${messages.c.CONFIRM_PASSWORD} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.CONFIRM_PASSWORD} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.CONFIRM_PASSWORD} ${messages.m.REQUIRED}`,
        'any.only': `${messages.m.INCORRECT_CONFIRM_PASSWORD}`,

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
    role: Joi.string()
      .valid("user", "admin")
      .messages({
        "string.base": `${messages.c.ROLE} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.ROLE} ${messages.m.EMPTY}`,
        "any.only": 'role باید یکی از مقادیر  "admin", "user" باشد',

        'string.base': `${messages.c.ID} ${messages.m.INCORRECT}`,
        'string.guid': `${messages.c.ID} ${messages.m.PATTERN}`,
        'string.empty': `${messages.c.ID} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.ID} ${messages.m.REQUIRED}`,
      }),
    role: Joi.string()
      .valid('user', 'admin')
      .messages({
        'string.base': `${messages.c.ROLE} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.ROLE} ${messages.m.EMPTY}`,
        'any.only': 'role باید یکی از مقادیر  "admin", "user" باشد',

      }),
    username: Joi.string()
      .min(5)
      .max(36)
      .messages({

        "string.base": `${messages.c.USERNAME} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.USERNAME} ${messages.m.EMPTY}`,
        "string.min": `${messages.c.USERNAME} ${messages.m.MINMAXLENGTH}`,
        "string.max": `${messages.c.USERNAME} ${messages.m.MINMAXLENGTH}`,
      }),
    profile: Joi.object({
      firstName: Joi.string().messages({
        "string.base": `${messages.c.FIRST_NAME} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.FIRST_NAME} ${messages.m.EMPTY}`,
      }),
      lastName: Joi.string().messages({
        "string.base": `${messages.c.LAST_NAME} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.LAST_NAME} ${messages.m.EMPTY}`,

        'string.base': `${messages.c.USERNAME} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.USERNAME} ${messages.m.EMPTY}`,
        'string.min': `${messages.c.USERNAME} ${messages.m.MINMAXLENGTH}`,
        'string.max': `${messages.c.USERNAME} ${messages.m.MINMAXLENGTH}`,
      }),
    profile: Joi.object({
      firstName: Joi.string().messages({
        'string.base': `${messages.c.FIRST_NAME} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.FIRST_NAME} ${messages.m.EMPTY}`,
      }),
      lastName: Joi.string().messages({
        'string.base': `${messages.c.LAST_NAME} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.LAST_NAME} ${messages.m.EMPTY}`,

      }),
      email: Joi.string()
        .email()
        .messages({

          "string.base": `${messages.c.EMAIL} ${messages.m.INCORRECT}`,
          "string.email": `${messages.c.EMAIL} ${messages.m.PATTERN}`,
          "string.empty": `${messages.c.EMAIL} ${messages.m.EMPTY}`,

          'string.base': `${messages.c.EMAIL} ${messages.m.INCORRECT}`,
          'string.email': `${messages.c.EMAIL} ${messages.m.PATTERN}`,
          'string.empty': `${messages.c.EMAIL} ${messages.m.EMPTY}`,

        }),
      phoneNumber: Joi.string()
        .pattern(/^(0)(9)[0-9]{9}$/)
        .messages({

          "string.base": `${messages.c.PHONE_NUMBER} ${messages.m.INCORRECT}`,
          "string.pattern.base": `${messages.c.PHONE_NUMBER} ${messages.m.PATTERN}`,
          "string.empty": `${messages.c.PHONE_NUMBER} ${messages.m.EMPTY}`,
        }),
    }).messages({
      "object.base": `${messages.c.PROFILE} ${messages.m.INCORRECT}`,

          'string.base': `${messages.c.PHONE_NUMBER} ${messages.m.INCORRECT}`,
          'string.pattern.base': `${messages.c.PHONE_NUMBER} ${messages.m.PATTERN}`,
          'string.empty': `${messages.c.PHONE_NUMBER} ${messages.m.EMPTY}`,
        }),
    }).messages({
      'object.base': `${messages.c.PROFILE} ${messages.m.INCORRECT}`,

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

        'string.base': `${messages.c.ID} ${messages.m.INCORRECT}`,
        'string.guid': `${messages.c.ID} ${messages.m.PATTERN}`,
        'string.empty': `${messages.c.ID} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.ID} ${messages.m.REQUIRED}`,

      }),
  }),

  login: Joi.object({
    username: Joi.string()
      .required()
      .messages({

        "string.base": `${messages.c.USERNAME} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.USERNAME} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.USERNAME} ${messages.m.REQUIRED}`,

        'string.base': `${messages.c.USERNAME} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.USERNAME} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.USERNAME} ${messages.m.REQUIRED}`,

      }),
    password: Joi.string()
      .required()
      .messages({

        "string.base": `${messages.c.PASSWORD} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.PASSWORD} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.PASSWORD} ${messages.m.REQUIRED}`,
      }),
  }),
};

        'string.base': `${messages.c.PASSWORD} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.PASSWORD} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.PASSWORD} ${messages.m.REQUIRED}`,
      }),
  }),
}

