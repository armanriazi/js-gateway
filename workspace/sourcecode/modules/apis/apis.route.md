
```javascript



import { Router } from "express";
import generalValidator, {
  generalValidatorArray,
} from "../../common/middlewares/generalValidator.js";
import paginationHandler from "../../common/middlewares/paginationHandler.js";
import controller from "./apis.controller.js";
import validator from "./apis.validator.js";
import auth from "../../services/auth.js";

const router = Router({ mergeParams: true });

router.post(
  "/",
  auth.check,
  auth.simpleCheckRoles(["superAdmin", "admin"]),
  generalValidatorArray(validator.create),
  controller.create
);

router.get(
  "/",
  auth.check,
  auth.simpleCheckRoles(["superAdmin", "admin"]),
  generalValidator(validator.index),
  paginationHandler,
  controller.index
);

router.get(
  "/:id",
  auth.check,
  auth.simpleCheckRoles(["superAdmin", "admin"]),
  generalValidator(validator.get),
  controller.getApiKeyService
);

router.patch(
  "/:id",
  auth.check,
  auth.simpleCheckRoles(["superAdmin", "admin"]),
  generalValidator(validator.update),
  controller.update
);

router.delete(
  "/:id",
  auth.check,
  auth.simpleCheckRoles(["superAdmin", "admin"]),
  generalValidator(validator.remove),
  controller.remove
);

export default router;
```