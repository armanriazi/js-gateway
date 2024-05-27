
```javascript



import { Router } from "express";
import generalValidator from "../../common/middlewares/generalValidator.js";
// import paginationHandler from '../../common/middlewares/paginationHandler.js'
import controller from "./apiKeys.controller.js";
import validator from "./apiKeys.validator.js";
import auth from "../../services/auth.js";
import paginationHandler from "../../common/middlewares/paginationHandler.js";

const router = Router({ mergeParams: true });

router.get(
  "/",
  auth.check,
  auth.simpleCheckRoles(["superAdmin", "admin"]),
  generalValidator(validator.index),
  paginationHandler,
  controller.index
);

// create apiKey
router.post(
  "/",
  auth.check,
  auth.simpleCheckRoles(["superAdmin", "admin"]),
  generalValidator(validator.create),
  auth.checkAdminCanCreateApiKey,
  controller.create
);

// update apiKey
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

router.get(
  "/:id",
  auth.check,
  auth.simpleCheckRoles(["superAdmin", "admin", "user"]),
  generalValidator(validator.get),
  controller.getApiKey
);

export default router;
```