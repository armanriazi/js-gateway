
```javascript



import Joi from "joi";
import messages from "../../common/messages.js";

export default {
  create: Joi.array()
    .items(
      Joi.object({
        ApiKeyId: Joi.string()
          .guid()
          .required()
          .messages({
            "string.base": `${messages.c.APIKEY_ID} ${messages.m.INCORRECT}`,
            "string.guid": `${messages.c.APIKEY_ID} ${messages.m.PATTERN}`,
            "string.empty": `${messages.c.APIKEY_ID} ${messages.m.EMPTY}`,
            "any.required": `${messages.c.APIKEY_ID} ${messages.m.REQUIRED}`,
          }),

        ServiceId: Joi.string()
          .guid()
          .required()
          .messages({
            "string.base": `${messages.c.SERVICE_ID} ${messages.m.INCORRECT}`,
            "string.guid": `${messages.c.SERVICE_ID} ${messages.m.PATTERN}`,
            "string.empty": `${messages.c.SERVICE_ID} ${messages.m.EMPTY}`,
            "any.required": `${messages.c.SERVICE_ID} ${messages.m.REQUIRED}`,
          }),
                .required()
                .items(
                  Joi.string()
                    .valid("get", "post")
                    .messages({
                      "string.base": `${messages.c.METHOD} ${messages.m.INCORRECT}`,
                      "string.empty": `${messages.c.METHOD} ${messages.m.EMPTY}`,
                    })
                )
                .messages({
                  "array.base": `${messages.c.METHOD} ${messages.m.MUST_BE_ARRAY}`,
                  "array.empty": `${messages.c.METHOD} ${messages.m.EMPTY}`,
                  "any.required": `${messages.c.METHOD} ${messages.m.REQUIRED}`,
                  "array.includesRequiredUnknowns": `${messages.c.METHOD} ${messages.m.EMPTY}`,
                  "any.only": `${messages.m.METHOD_VALID_FORMAT}`,
                }),
              path: Joi.string()
                .required()
                .messages({
                  "string.base": `${messages.c.PATH} ${messages.m.INCORRECT}`,
                  "string.empty": `${messages.c.PATH} ${messages.m.EMPTY}`,
                  "any.required": `${messages.c.PATH} ${messages.m.REQUIRED}`,
                }),
            })
          )
          .messages({
            "array.base": `${messages.c.APIS} ${messages.m.MUST_BE_ARRAY}`,
            "array.empty": `${messages.c.APIS} ${messages.m.EMPTY}`,
            "any.required": `${messages.c.APIS} ${messages.m.REQUIRED}`,
          }),

        TPM: Joi.number()
          .integer()
          .required()
          .messages({
            "number.base": `${messages.c.MAX_COUNT_TPM} ${messages.m.INCORRECT}`,
            "number.integer": `${messages.c.MAX_COUNT_TPM} ${messages.m.PATTERN}`,
            "number.empty": `${messages.c.MAX_COUNT_TPM} ${messages.m.EMPTY}`,
            "any.required": `${messages.c.MAX_COUNT_TPM} ${messages.m.REQUIRED}`,
          }),
        maxCount: Joi.number()
          .integer()
          .messages({
            "number.base": `${messages.c.MAX_COUNT} ${messages.m.INCORRECT}`,
            "number.integer": `${messages.c.MAX_COUNT} ${messages.m.PATTERN}`,
          }),

        policies: Joi.object({
          image: Joi.object({
            size: Joi.number()
              .min(0)
              .integer()
              .messages({
                "number.base": `${messages.c.IMAGE_SIZE_POLICY} ${messages.m.INCORRECT}`,
                "number.integer": `${messages.c.IMAGE_SIZE_POLICY} ${messages.m.PATTERN}`,
                "number.empty": `${messages.c.IMAGE_SIZE_POLICY} ${messages.m.EMPTY}`,
                "number.unsafe": `${messages.c.IMAGE_SIZE_POLICY} ${messages.m.GREATER_THEN_ALLOWED}`,
                "number.min": `${messages.c.IMAGE_SIZE_POLICY} ${messages.m.NOT_BE_NEGATIVE}`,
              }),
          }).messages({
            "string.base": `${messages.c.IMAGE_POLICY} ${messages.m.INCORRECT}`,
            "string.empty": `${messages.c.IMAGE_POLICY} ${messages.m.EMPTY}`,
          }),
          video: Joi.object({
            size: Joi.number()
              .min(0)
              .integer()
              .messages({
                "number.base": `${messages.c.VIDEO_SIZE_POLICY} ${messages.m.INCORRECT}`,
                "number.integer": `${messages.c.VIDEO_SIZE_POLICY} ${messages.m.PATTERN}`,
                "number.empty": `${messages.c.VIDEO_SIZE_POLICY} ${messages.m.EMPTY}`,
                "number.unsafe": `${messages.c.VIDEO_SIZE_POLICY} ${messages.m.GREATER_THEN_ALLOWED}`,
                "number.min": `${messages.c.VIDEO_SIZE_POLICY} ${messages.m.NOT_BE_NEGATIVE}`,
              }),
            duration: Joi.number()
              .min(0)
              .integer()
              .messages({
                "number.base": `${messages.c.VIDEO_DURATION_POLICY} ${messages.m.INCORRECT}`,
                "number.integer": `${messages.c.VIDEO_DURATION_POLICY} ${messages.m.PATTERN}`,
                "number.empty": `${messages.c.VIDEO_DURATION_POLICY} ${messages.m.EMPTY}`,
                "number.unsafe": `${messages.c.VIDEO_DURATION_POLICY} ${messages.m.GREATER_THEN_ALLOWED}`,
                "number.min": `${messages.c.VIDEO_DURATION_POLICY} ${messages.m.NOT_BE_NEGATIVE}`,
              }),
          }).messages({
            "string.base": `${messages.c.VIDEO_POLICY} ${messages.m.INCORRECT}`,
            "string.empty": `${messages.c.VIDEO_POLICY} ${messages.m.EMPTY}`,
          }),
          voice: Joi.object({
            size: Joi.number()
              .min(0)
              .integer()
              .messages({
                "number.base": `${messages.c.VOICE_SIZE_POLICY} ${messages.m.INCORRECT}`,
                "number.integer": `${messages.c.VOICE_SIZE_POLICY} ${messages.m.PATTERN}`,
                "number.empty": `${messages.c.VOICE_SIZE_POLICY} ${messages.m.EMPTY}`,
                "number.unsafe": `${messages.c.VOICE_SIZE_POLICY} ${messages.m.GREATER_THEN_ALLOWED}`,
                "number.min": `${messages.c.VOICE_SIZE_POLICY} ${messages.m.NOT_BE_NEGATIVE}`,
              }),
            duration: Joi.number()
              .min(0)
              .integer()
              .messages({
                "number.base": `${messages.c.VOICE_DURATION_POLICY} ${messages.m.INCORRECT}`,
                "number.integer": `${messages.c.VOICE_DURATION_POLICY} ${messages.m.PATTERN}`,
                "number.empty": `${messages.c.VOICE_DURATION_POLICY} ${messages.m.EMPTY}`,
                "number.unsafe": `${messages.c.VOICE_DURATION_POLICY} ${messages.m.GREATER_THEN_ALLOWED}`,
                "number.min": `${messages.c.VOICE_DURATION_POLICY} ${messages.m.NOT_BE_NEGATIVE}`,
              }),
          }).messages({
            "string.base": `${messages.c.VOICE_POLICY} ${messages.m.INCORRECT}`,
            "string.empty": `${messages.c.VOICE_POLICY} ${messages.m.EMPTY}`,
            "any.required": `${messages.c.VOICE_POLICY} ${messages.m.REQUIRED}`,
          }),
          character: Joi.object({
            counter: Joi.number()
              .min(0)
              .integer()
              .messages({
                "number.base": `${messages.c.CHARACTER_COUNT_POLICY} ${messages.m.INCORRECT}`,
                "number.integer": `${messages.c.CHARACTER_COUNT_POLICY} ${messages.m.PATTERN}`,
                "number.empty": `${messages.c.CHARACTER_COUNT_POLICY} ${messages.m.EMPTY}`,
                "any.required": `${messages.c.CHARACTER_COUNT_POLICY} ${messages.m.REQUIRED}`,
                "number.unsafe": `${messages.c.CHARACTER_COUNT_POLICY} ${messages.m.GREATER_THEN_ALLOWED}`,
                "number.min": `${messages.c.CHARACTER_COUNT_POLICY} ${messages.m.NOT_BE_NEGATIVE}`,
              }),
          }).messages({
            "string.base": `${messages.c.CHARACTER_POLICY} ${messages.m.INCORRECT}`,
            "string.empty": `${messages.c.CHARACTER_POLICY} ${messages.m.EMPTY}`,
            "any.required": `${messages.c.CHARACTER_POLICY} ${messages.m.REQUIRED}`,
          }),
        }).messages({
          "object.base": `${messages.c.POLICIES} ${messages.m.INCORRECT}`,
          "object.empty": `${messages.c.POLICIES} ${messages.m.EMPTY}`,
          "any.required": `${messages.c.POLICIES} ${messages.m.REQUIRED}`,
        }),
      })
    )
    .messages({
      "array.base": `${messages.c.INPUT_DATA} ${messages.m.MUST_BE_ARRAY}`,
      "array.empty": messages.m.ARRAY_INCLUDES,
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

  update: Joi.object({
    id: Joi.string()
      .guid()
      .messages({
        "string.base": `${messages.c.ID} ${messages.m.INCORRECT}`,
        "string.guid": `${messages.c.ID} ${messages.m.PATTERN}`,
        "string.empty": `${messages.c.ID} ${messages.m.EMPTY}`,
      }),
    apis: Joi.array()
      .items(
        Joi.object({
          method: Joi.array()
            .items(
              Joi.string()
                .valid("get", "post")
                .messages({
                  "string.base": `${messages.c.METHOD} ${messages.m.INCORRECT}`,
                  "string.empty": `${messages.c.METHOD} ${messages.m.EMPTY}`,
                })
            )
            .messages({
              "array.base": `${messages.c.METHOD} ${messages.m.MUST_BE_ARRAY}`,
              "array.empty": `${messages.c.METHOD} ${messages.m.EMPTY}`,
              "any.only": `${messages.m.METHOD_VALID_FORMAT}`,
            }),

          path: Joi.string().messages({
            "string.base": `${messages.c.PATH} ${messages.m.INCORRECT}`,
            "string.empty": `${messages.c.PATH} ${messages.m.EMPTY}`,
          }),
        })
      )
      .messages({
        "array.base": `${messages.c.APIS} ${messages.m.MUST_BE_ARRAY}`,
        "array.empty": `${messages.c.APIS} ${messages.m.EMPTY}`,
      }),

    ApiKeyId: Joi.string()
      .guid()
      .messages({
        "string.base": `${messages.c.APIKEY_ID} ${messages.m.INCORRECT}`,
        "string.guid": `${messages.c.APIKEY_ID} ${messages.m.PATTERN}`,
        "string.empty": `${messages.c.APIKEY_ID} ${messages.m.EMPTY}`,
      }),

    ServiceId: Joi.string()
      .guid()
      .messages({
        "string.base": `${messages.c.SERVICE_ID} ${messages.m.INCORRECT}`,
        "string.guid": `${messages.c.SERVICE_ID} ${messages.m.PATTERN}`,
        "string.empty": `${messages.c.SERVICE_ID} ${messages.m.EMPTY}`,
      }),

    TPM: Joi.number()
      .integer()
      .messages({
        "number.base": `${messages.c.MAX_COUNT_TPM} ${messages.m.INCORRECT}`,
        "number.integer": `${messages.c.MAX_COUNT_TPM} ${messages.m.PATTERN}`,
        "number.empty": `${messages.c.MAX_COUNT_TPM} ${messages.m.EMPTY}`,
      }),
    maxCount: Joi.number()
      .integer()
      .messages({
        "number.base": `${messages.c.MAX_COUNT} ${messages.m.INCORRECT}`,
        "number.integer": `${messages.c.MAX_COUNT} ${messages.m.PATTERN}`,
      }),

    policies: Joi.object().messages({
      "object.base": `${messages.c.POLICIES} ${messages.m.INCORRECT}`,
      "object.empty": `${messages.c.POLICIES} ${messages.m.EMPTY}`,
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
```