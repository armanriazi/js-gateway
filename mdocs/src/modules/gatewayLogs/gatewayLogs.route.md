
```javascript



import { Router } from "express";
import generalValidator from "../../common/middlewares/generalValidator.js";
import controller from "./gatewayLogs.controller.js";
import validator from "./gatewayLogs.validator.js";
import auth from "../../services/auth.js";

const router = Router({ mergeParams: true });

router.get(
  "/:id",
  auth.check,
  auth.simpleCheckRoles(["superAdmin", "admin"]),
  generalValidator(validator.get),
  controller.get
);

export default router;
```