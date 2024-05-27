
import Joi from "joi";
import messages from "../../common/messages.js";

import Joi from 'joi'
import messages from '../../common/messages'
>>>>>>> origin/armanriazi

export default {
  create: Joi.object({
    name: Joi.string()
      .required()
      .messages({

        "string.base": `${messages.c.SERVICE_NAME} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.SERVICE_NAME} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.SERVICE_NAME} ${messages.m.REQUIRED}`,

        'string.base': `${messages.c.SERVICE_NAME} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.SERVICE_NAME} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.SERVICE_NAME} ${messages.m.REQUIRED}`,
>>>>>>> origin/armanriazi
      }),

    ProjectId: Joi.string()
      .guid()
      .required()
      .messages({

        "string.base": `${messages.c.PROJECT_ID} ${messages.m.INCORRECT}`,
        "string.guid": `${messages.c.PROJECT_ID} ${messages.m.PATTERN}`,
        "string.empty": `${messages.c.PROJECT_ID} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.PROJECT_ID} ${messages.m.REQUIRED}`,

        'string.base': `${messages.c.PROJECT_ID} ${messages.m.INCORRECT}`,
        'string.guid': `${messages.c.PROJECT_ID} ${messages.m.PATTERN}`,
        'string.empty': `${messages.c.PROJECT_ID} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.PROJECT_ID} ${messages.m.REQUIRED}`,
>>>>>>> origin/armanriazi
      }),

    validation: Joi.object()
      .required()
      .messages({

        "object.base": `${messages.c.VALIDATION} ${messages.m.INCORRECT}`,
        "object.empty": `${messages.c.VALIDATION} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.VALIDATION} ${messages.m.REQUIRED}`,

        'object.base': `${messages.c.VALIDATION} ${messages.m.INCORRECT}`,
        'object.empty': `${messages.c.VALIDATION} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.VALIDATION} ${messages.m.REQUIRED}`,
>>>>>>> origin/armanriazi
      }),

    apis: Joi.array()
      .required()
      .items(
        Joi.object({
          method: Joi.array()
            .required()
            .items(
              Joi.string()
                .required()

                .valid("get", "post")
                .required()
                .messages({
                  "string.base": `${messages.c.METHOD} ${messages.m.INCORRECT}`,
                  "string.empty": `${messages.c.METHOD} ${messages.m.EMPTY}`,
                })
            )
            .messages({
              "array.base": `${messages.c.METHOD} ${messages.m.MUST_BE_ARRAY}`,
              "array.empty": `${messages.c.METHOD} ${messages.m.EMPTY}`,
              "array.includesRequiredUnknowns": `${messages.c.METHOD} ${messages.m.EMPTY}`,
              "any.required": `${messages.c.METHOD} ${messages.m.REQUIRED}`,
              "any.only": `${messages.m.METHOD_VALID_FORMAT}`,

                .valid('get', 'post')
                .required()
                .messages({
                  'string.base': `${messages.c.METHOD} ${messages.m.INCORRECT}`,
                  'string.empty': `${messages.c.METHOD} ${messages.m.EMPTY}`,
                }),
            )
            .messages({
              'array.base': `${messages.c.METHOD} ${messages.m.MUST_BE_ARRAY}`,
              'array.empty': `${messages.c.METHOD} ${messages.m.EMPTY}`,
              'array.includesRequiredUnknowns': `${messages.c.METHOD} ${messages.m.EMPTY}`,
              'any.required': `${messages.c.METHOD} ${messages.m.REQUIRED}`,
              'any.only': `${messages.m.METHOD_VALID_FORMAT}`,
>>>>>>> origin/armanriazi
            }),
          path: Joi.string()
            .required()
            .messages({

              "string.base": `${messages.c.PATH} ${messages.m.INCORRECT}`,
              "string.empty": `${messages.c.PATH} ${messages.m.EMPTY}`,
              "any.required": `${messages.c.PATH} ${messages.m.REQUIRED}`,
            }),
          files: Joi.array()
            .empty(["", null])

              'string.base': `${messages.c.PATH} ${messages.m.INCORRECT}`,
              'string.empty': `${messages.c.PATH} ${messages.m.EMPTY}`,
              'any.required': `${messages.c.PATH} ${messages.m.REQUIRED}`,
            }),
          files: Joi.array()
            .empty(['', null])
>>>>>>> origin/armanriazi
            .default(null)
            .items(
              Joi.object({
                name: Joi.string()
                  .required()
                  .messages({

                    "string.base": `${messages.c.FILENAME} ${messages.m.INCORRECT}`,
                    "string.empty": `${messages.c.FILENAME} ${messages.m.EMPTY}`,
                    "any.required": `${messages.c.FILENAME} ${messages.m.REQUIRED}`,
                  }),
                type: Joi.string()
                  .required()
                  .valid("image", "video", "voice")
                  .messages({
                    "string.base": `${messages.c.TYPE} ${messages.m.INCORRECT}`,
                    "string.empty": `${messages.c.TYPE} ${messages.m.EMPTY}`,
                    "any.required": `${messages.c.TYPE} ${messages.m.REQUIRED}`,
                    "any.only": messages.m.ENTRY_VALID_FILE_TYPE,
                  }),
                howGet: Joi.string()
                  .required()
                  .valid("file", "link", "base64")
                  .messages({
                    "string.base": `${messages.c.HOWGET} ${messages.m.INCORRECT}`,
                    "string.empty": `${messages.c.HOWGET} ${messages.m.EMPTY}`,
                    "any.required": `${messages.c.HOWGET} ${messages.m.REQUIRED}`,
                    "any.only": messages.m.ENTRY_VALID_HOWGET,

                    'string.base': `${messages.c.FILENAME} ${messages.m.INCORRECT}`,
                    'string.empty': `${messages.c.FILENAME} ${messages.m.EMPTY}`,
                    'any.required': `${messages.c.FILENAME} ${messages.m.REQUIRED}`,
                  }),
                type: Joi.string()
                  .required()
                  .valid('image', 'video', 'voice')
                  .messages({
                    'string.base': `${messages.c.TYPE} ${messages.m.INCORRECT}`,
                    'string.empty': `${messages.c.TYPE} ${messages.m.EMPTY}`,
                    'any.required': `${messages.c.TYPE} ${messages.m.REQUIRED}`,
                    'any.only': messages.m.ENTRY_VALID_FILE_TYPE,
                  }),
                howGet: Joi.string()
                  .required()
                  .valid('file', 'link', 'base64')
                  .messages({
                    'string.base': `${messages.c.HOWGET} ${messages.m.INCORRECT}`,
                    'string.empty': `${messages.c.HOWGET} ${messages.m.EMPTY}`,
                    'any.required': `${messages.c.HOWGET} ${messages.m.REQUIRED}`,
                    'any.only': messages.m.ENTRY_VALID_HOWGET,
>>>>>>> origin/armanriazi
                  }),
                required: Joi.boolean()
                  .default(false)
                  .messages({

                    "boolean.base": `${messages.c.REQUIRED_FILE} ${messages.m.INCORRECT}`,
                    "boolean.empty": `${messages.c.REQUIRED_FILE} ${messages.m.EMPTY}`,
                  }),
              })
            ),
        })
      )
      .messages({
        "array.base": `${messages.c.APIS} ${messages.m.MUST_BE_ARRAY}`,
        "array.empty": `${messages.c.APIS} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.APIS} ${messages.m.REQUIRED}`,

                    'boolean.base': `${messages.c.REQUIRED_FILE} ${messages.m.INCORRECT}`,
                    'boolean.empty': `${messages.c.REQUIRED_FILE} ${messages.m.EMPTY}`,
                  }),
              }),
            ),
        }),
      )
      .messages({
        'array.base': `${messages.c.APIS} ${messages.m.MUST_BE_ARRAY}`,
        'array.empty': `${messages.c.APIS} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.APIS} ${messages.m.REQUIRED}`,
>>>>>>> origin/armanriazi
      }),

    type: Joi.string()
      .required()

      .valid("proxy", "direct")
      .messages({
        "string.base": `${messages.c.REQUEST_TYPE} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.REQUEST_TYPE} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.REQUEST_TYPE} ${messages.m.REQUIRED}`,
        "any.only": `${messages.c.REQUEST_TYPE} ${messages.m.TYPE_ERROR}`,
      }),

    host: Joi.string()
      .when("type", {
        is: "proxy",

      .valid('proxy', 'direct')
      .messages({
        'string.base': `${messages.c.REQUEST_TYPE} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.REQUEST_TYPE} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.REQUEST_TYPE} ${messages.m.REQUIRED}`,
        'any.only': `${messages.c.REQUEST_TYPE} ${messages.m.TYPE_ERROR}`,
      }),

    host: Joi.string()
      .when('type', {
        is: 'proxy',
>>>>>>> origin/armanriazi
        then: Joi.string().required(),
        otherwise: Joi.string().forbidden(),
      })
      .messages({

        "string.base": `${messages.c.HOST} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.HOST} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.HOST} ${messages.m.REQUIRED}`,
        "any.unknown": `${messages.c.HOST} ${messages.m.IS_NOT_ALLOWED}`,
      }),

    protocol: Joi.string()
      .when("type", {
        is: "proxy",
        then: Joi.string().valid("http", "https").required(),
        otherwise: Joi.string().forbidden(),
      })
      .messages({
        "string.base": `${messages.c.PROTOCOL} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.PROTOCOL} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.PROTOCOL} ${messages.m.REQUIRED}`,
        "any.unknown": `${messages.c.PROTOCOL} ${messages.m.IS_NOT_ALLOWED}`,
        "any.only": `${messages.c.PROTOCOL} ${messages.m.PROTOCOL_ERROR}`,
      }),

    rejectUnauthorized: Joi.boolean()
      .when("protocol", {
        is: "https",

        'string.base': `${messages.c.HOST} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.HOST} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.HOST} ${messages.m.REQUIRED}`,
        'any.unknown': `${messages.c.HOST} ${messages.m.IS_NOT_ALLOWED}`,
      }),

    protocol: Joi.string()
      .when('type', {
        is: 'proxy',
        then: Joi.string().valid('http', 'https').required(),
        otherwise: Joi.string().forbidden(),
      })
      .messages({
        'string.base': `${messages.c.PROTOCOL} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.PROTOCOL} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.PROTOCOL} ${messages.m.REQUIRED}`,
        'any.unknown': `${messages.c.PROTOCOL} ${messages.m.IS_NOT_ALLOWED}`,
        'any.only': `${messages.c.PROTOCOL} ${messages.m.PROTOCOL_ERROR}`,
      }),

    rejectUnauthorized: Joi.boolean()
      .when('protocol', {
        is: 'https',
>>>>>>> origin/armanriazi
        then: Joi.boolean().required(),
        otherwise: Joi.boolean().forbidden(),
      })
      .messages({

        "boolean.base": `${messages.c.REJECT_UNAUTHORIZED} ${messages.m.INCORRECT}`,
        "boolean.empty": `${messages.c.REJECT_UNAUTHORIZED} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.REJECT_UNAUTHORIZED} ${messages.m.REQUIRED}`,
        "any.unknown": `${messages.c.REJECT_UNAUTHORIZED} ${messages.m.IS_NOT_ALLOWED}`,
        "any.only": `${messages.c.REJECT_UNAUTHORIZED} ${messages.m.REJECT_UNAUTHORIZED_ERROR}`,
      }),

    queue: Joi.string()
      .when("type", {
        is: "direct",

        'boolean.base': `${messages.c.REJECT_UNAUTHORIZED} ${messages.m.INCORRECT}`,
        'boolean.empty': `${messages.c.REJECT_UNAUTHORIZED} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.REJECT_UNAUTHORIZED} ${messages.m.REQUIRED}`,
        'any.unknown': `${messages.c.REJECT_UNAUTHORIZED} ${messages.m.IS_NOT_ALLOWED}`,
        'any.only': `${messages.c.REJECT_UNAUTHORIZED} ${messages.m.REJECT_UNAUTHORIZED_ERROR}`,
      }),

    queue: Joi.string()
      .when('type', {
        is: 'direct',
>>>>>>> origin/armanriazi
        then: Joi.string().required(),
        otherwise: Joi.string().forbidden(),
      })
      .messages({

        "string.base": `${messages.c.QUEUE} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.QUEUE} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.QUEUE} ${messages.m.REQUIRED}`,
        "any.unknown": `${messages.c.QUEUE} ${messages.m.IS_NOT_ALLOWED}`,

        'string.base': `${messages.c.QUEUE} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.QUEUE} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.QUEUE} ${messages.m.REQUIRED}`,
        'any.unknown': `${messages.c.QUEUE} ${messages.m.IS_NOT_ALLOWED}`,
>>>>>>> origin/armanriazi
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
>>>>>>> origin/armanriazi
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
>>>>>>> origin/armanriazi
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
>>>>>>> origin/armanriazi
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

    name: Joi.string().messages({
      "string.base": `${messages.c.SERVICE_NAME} ${messages.m.INCORRECT}`,
      "string.empty": `${messages.c.SERVICE_NAME} ${messages.m.EMPTY}`,

        'string.base': `${messages.c.ID} ${messages.m.INCORRECT}`,
        'string.guid': `${messages.c.ID} ${messages.m.PATTERN}`,
        'string.empty': `${messages.c.ID} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.ID} ${messages.m.REQUIRED}`,
      }),

    name: Joi.string().messages({
      'string.base': `${messages.c.SERVICE_NAME} ${messages.m.INCORRECT}`,
      'string.empty': `${messages.c.SERVICE_NAME} ${messages.m.EMPTY}`,
>>>>>>> origin/armanriazi
    }),

    apis: Joi.array()
      .items(
        Joi.object({
          method: Joi.array()
            .required()
            .items(
              Joi.string()
                .required()

                .valid("get", "post")
                .required()
                .messages({
                  "string.base": `${messages.c.METHOD} ${messages.m.INCORRECT}`,
                  "string.empty": `${messages.c.METHOD} ${messages.m.EMPTY}`,
                })
            )
            .messages({
              "array.base": `${messages.c.METHOD} ${messages.m.MUST_BE_ARRAY}`,
              "array.empty": `${messages.c.METHOD} ${messages.m.EMPTY}`,
              "array.includesRequiredUnknowns": `${messages.c.METHOD} ${messages.m.EMPTY}`,
              "any.required": `${messages.c.METHOD} ${messages.m.REQUIRED}`,
              "any.only": `${messages.m.METHOD_VALID_FORMAT}`,

                .valid('get', 'post')
                .required()
                .messages({
                  'string.base': `${messages.c.METHOD} ${messages.m.INCORRECT}`,
                  'string.empty': `${messages.c.METHOD} ${messages.m.EMPTY}`,
                }),
            )
            .messages({
              'array.base': `${messages.c.METHOD} ${messages.m.MUST_BE_ARRAY}`,
              'array.empty': `${messages.c.METHOD} ${messages.m.EMPTY}`,
              'array.includesRequiredUnknowns': `${messages.c.METHOD} ${messages.m.EMPTY}`,
              'any.required': `${messages.c.METHOD} ${messages.m.REQUIRED}`,
              'any.only': `${messages.m.METHOD_VALID_FORMAT}`,
>>>>>>> origin/armanriazi
            }),
          path: Joi.string()
            .required()
            .messages({

              "string.base": `${messages.c.PATH} ${messages.m.INCORRECT}`,
              "string.empty": `${messages.c.PATH} ${messages.m.EMPTY}`,
              "any.required": `${messages.c.PATH} ${messages.m.REQUIRED}`,

              'string.base': `${messages.c.PATH} ${messages.m.INCORRECT}`,
              'string.empty': `${messages.c.PATH} ${messages.m.EMPTY}`,
              'any.required': `${messages.c.PATH} ${messages.m.REQUIRED}`,
>>>>>>> origin/armanriazi
            }),
          files: Joi.array().items(
            Joi.object({
              name: Joi.string()
                .required()
                .messages({

                  "string.base": `${messages.c.FILENAME} ${messages.m.INCORRECT}`,
                  "string.empty": `${messages.c.FILENAME} ${messages.m.EMPTY}`,
                  "any.required": `${messages.c.FILENAME} ${messages.m.REQUIRED}`,
                }),
              type: Joi.string()
                .required()
                .valid("image", "video", "voice")
                .messages({
                  "string.base": `${messages.c.TYPE} ${messages.m.INCORRECT}`,
                  "string.empty": `${messages.c.TYPE} ${messages.m.EMPTY}`,
                  "any.required": `${messages.c.TYPE} ${messages.m.REQUIRED}`,
                  "any.only": messages.m.ENTRY_VALID_FILE_TYPE,
                }),
              howGet: Joi.string()
                .required()
                .valid("file", "link", "base64")
                .messages({
                  "string.base": `${messages.c.HOWGET} ${messages.m.INCORRECT}`,
                  "string.empty": `${messages.c.HOWGET} ${messages.m.EMPTY}`,
                  "any.required": `${messages.c.HOWGET} ${messages.m.REQUIRED}`,
                  "any.only": messages.m.ENTRY_VALID_HOWGET,

                  'string.base': `${messages.c.FILENAME} ${messages.m.INCORRECT}`,
                  'string.empty': `${messages.c.FILENAME} ${messages.m.EMPTY}`,
                  'any.required': `${messages.c.FILENAME} ${messages.m.REQUIRED}`,
                }),
              type: Joi.string()
                .required()
                .valid('image', 'video', 'voice')
                .messages({
                  'string.base': `${messages.c.TYPE} ${messages.m.INCORRECT}`,
                  'string.empty': `${messages.c.TYPE} ${messages.m.EMPTY}`,
                  'any.required': `${messages.c.TYPE} ${messages.m.REQUIRED}`,
                  'any.only': messages.m.ENTRY_VALID_FILE_TYPE,
                }),
              howGet: Joi.string()
                .required()
                .valid('file', 'link', 'base64')
                .messages({
                  'string.base': `${messages.c.HOWGET} ${messages.m.INCORRECT}`,
                  'string.empty': `${messages.c.HOWGET} ${messages.m.EMPTY}`,
                  'any.required': `${messages.c.HOWGET} ${messages.m.REQUIRED}`,
                  'any.only': messages.m.ENTRY_VALID_HOWGET,
>>>>>>> origin/armanriazi
                }),
              required: Joi.boolean()
                .default(false)
                .messages({

                  "boolean.base": `${messages.c.REQUIRED_FILE} ${messages.m.INCORRECT}`,
                  "boolean.empty": `${messages.c.REQUIRED_FILE} ${messages.m.EMPTY}`,
                }),
            })
          ),
        })
      )
      .messages({
        "array.base": `${messages.c.APIS} ${messages.m.MUST_BE_ARRAY}`,
        "array.empty": `${messages.c.APIS} ${messages.m.EMPTY}`,

                  'boolean.base': `${messages.c.REQUIRED_FILE} ${messages.m.INCORRECT}`,
                  'boolean.empty': `${messages.c.REQUIRED_FILE} ${messages.m.EMPTY}`,
                }),
            }),
          ),
        }),
      )
      .messages({
        'array.base': `${messages.c.APIS} ${messages.m.MUST_BE_ARRAY}`,
        'array.empty': `${messages.c.APIS} ${messages.m.EMPTY}`,
>>>>>>> origin/armanriazi
      }),

    ProjectId: Joi.string()
      .guid()
      .messages({

        "string.base": `${messages.c.PROJECT_ID} ${messages.m.INCORRECT}`,
        "string.guid": `${messages.c.PROJECT_ID} ${messages.m.PATTERN}`,
        "string.empty": `${messages.c.PROJECT_ID} ${messages.m.EMPTY}`,
      }),

    validation: Joi.object().messages({
      "object.base": `${messages.c.VALIDATION} ${messages.m.INCORRECT}`,
      "object.empty": `${messages.c.VALIDATION} ${messages.m.EMPTY}`,
    }),

    queue: Joi.string()
      .when("type", {
        is: "direct",

        'string.base': `${messages.c.PROJECT_ID} ${messages.m.INCORRECT}`,
        'string.guid': `${messages.c.PROJECT_ID} ${messages.m.PATTERN}`,
        'string.empty': `${messages.c.PROJECT_ID} ${messages.m.EMPTY}`,
      }),

    validation: Joi.object().messages({
      'object.base': `${messages.c.VALIDATION} ${messages.m.INCORRECT}`,
      'object.empty': `${messages.c.VALIDATION} ${messages.m.EMPTY}`,
    }),

    queue: Joi.string()
      .when('type', {
        is: 'direct',
>>>>>>> origin/armanriazi
        then: Joi.string().required(),
        otherwise: Joi.string(),
      })
      .messages({

        "string.base": `${messages.c.QUEUE} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.QUEUE} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.QUEUE} ${messages.m.REQUIRED}`,
      }),

    host: Joi.string()
      .when("type", {
        is: "proxy",

        'string.base': `${messages.c.QUEUE} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.QUEUE} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.QUEUE} ${messages.m.REQUIRED}`,
      }),

    host: Joi.string()
      .when('type', {
        is: 'proxy',
>>>>>>> origin/armanriazi
        then: Joi.string().required(),
        otherwise: Joi.string(),
      })
      .messages({

        "string.base": `${messages.c.HOST} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.HOST} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.HOST} ${messages.m.REQUIRED}`,
      }),

    protocol: Joi.string()
      .when("type", {
        is: "proxy",
        then: Joi.string().valid("http", "https").required(),
        otherwise: Joi.string().valid("http", "https"),
      })
      .messages({
        "string.base": `${messages.c.PROTOCOL} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.PROTOCOL} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.PROTOCOL} ${messages.m.REQUIRED}`,
        "any.only": `${messages.c.PROTOCOL} ${messages.m.PROTOCOL_ERROR}`,
      }),

    type: Joi.string()
      .valid("proxy", "direct")
      .messages({
        "string.base": `${messages.c.REQUEST_TYPE} ${messages.m.INCORRECT}`,
        "string.empty": `${messages.c.REQUEST_TYPE} ${messages.m.EMPTY}`,
        "any.only": `${messages.c.REQUEST_TYPE} ${messages.m.TYPE_ERROR}`,
      }),

    rejectUnauthorized: Joi.boolean()
      .when("protocol", {
        is: "https",

        'string.base': `${messages.c.HOST} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.HOST} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.HOST} ${messages.m.REQUIRED}`,
      }),

    protocol: Joi.string()
      .when('type', {
        is: 'proxy',
        then: Joi.string().valid('http', 'https').required(),
        otherwise: Joi.string().valid('http', 'https'),
      })
      .messages({
        'string.base': `${messages.c.PROTOCOL} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.PROTOCOL} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.PROTOCOL} ${messages.m.REQUIRED}`,
        'any.only': `${messages.c.PROTOCOL} ${messages.m.PROTOCOL_ERROR}`,
      }),

    type: Joi.string()
      .valid('proxy', 'direct')
      .messages({
        'string.base': `${messages.c.REQUEST_TYPE} ${messages.m.INCORRECT}`,
        'string.empty': `${messages.c.REQUEST_TYPE} ${messages.m.EMPTY}`,
        'any.only': `${messages.c.REQUEST_TYPE} ${messages.m.TYPE_ERROR}`,
      }),

    rejectUnauthorized: Joi.boolean()
      .when('protocol', {
        is: 'https',
>>>>>>> origin/armanriazi
        then: Joi.boolean().required(),
        otherwise: Joi.boolean().valid(false),
      })
      .messages({

        "boolean.base": `${messages.c.REJECT_UNAUTHORIZED} ${messages.m.INCORRECT}`,
        "boolean.empty": `${messages.c.REJECT_UNAUTHORIZED} ${messages.m.EMPTY}`,
        "any.required": `${messages.c.REJECT_UNAUTHORIZED} ${messages.m.REQUIRED}`,
        "any.only": `${messages.c.REJECT_UNAUTHORIZED} ${messages.m.REJECT_UNAUTHORIZED_ERROR}`,

        'boolean.base': `${messages.c.REJECT_UNAUTHORIZED} ${messages.m.INCORRECT}`,
        'boolean.empty': `${messages.c.REJECT_UNAUTHORIZED} ${messages.m.EMPTY}`,
        'any.required': `${messages.c.REJECT_UNAUTHORIZED} ${messages.m.REQUIRED}`,
        'any.only': `${messages.c.REJECT_UNAUTHORIZED} ${messages.m.REJECT_UNAUTHORIZED_ERROR}`,
>>>>>>> origin/armanriazi
      }),
  }),

  remove: Joi.object({
    id: Joi.string()
      .guid()
      .messages({

        "string.base": `${messages.c.ID} ${messages.m.INCORRECT}`,
        "string.guid": `${messages.c.ID} ${messages.m.PATTERN}`,
        "string.empty": `${messages.c.ID} ${messages.m.EMPTY}`,
      }),
  }),
};

        'string.base': `${messages.c.ID} ${messages.m.INCORRECT}`,
        'string.guid': `${messages.c.ID} ${messages.m.PATTERN}`,
        'string.empty': `${messages.c.ID} ${messages.m.EMPTY}`,
      }),
  }),
}
>>>>>>> origin/armanriazi
