
```javascript



import Joi from "joi";
import messages from "../../common/messages.js";

export default {
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
};
```