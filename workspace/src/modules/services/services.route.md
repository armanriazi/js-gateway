
import { Router } from "express";
import generalValidator from "../../common/middlewares/generalValidator.js";
import paginationHandler from "../../common/middlewares/paginationHandler.js";
import controller from "./services.controller.js";
import validator from "./services.validator.js";
import auth from "../../services/auth.js";

const router = Router({ mergeParams: true });

router.post(
  "/",
  auth.check,
  auth.simpleCheckRoles(["superAdmin", "admin"]),
  generalValidator(validator.create),
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
  controller.getService
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

import { Router } from 'express'
import generalValidator from '../../common/middlewares/generalValidator'
import paginationHandler from '../../common/middlewares/paginationHandler'
import controller from './services.controller'
import validator from './services.validator'
import auth from '../../services/auth'

const router = Router({ mergeParams: true })

router.post(
  '/',
  auth.check,
  auth.simpleCheckRoles(['superAdmin', 'admin']),
  generalValidator(validator.create),
  controller.create
)

router.get(
  '/',
  auth.check,
  auth.simpleCheckRoles(['superAdmin', 'admin']),
  generalValidator(validator.index),
  paginationHandler,
  controller.index
)

router.get(
  '/:id',
  auth.check,
  auth.simpleCheckRoles(['superAdmin', 'admin']),
  generalValidator(validator.get),
  controller.getService
)

router.patch(
  '/:id',
  auth.check,
  auth.simpleCheckRoles(['superAdmin', 'admin']),
  generalValidator(validator.update),
  controller.update
)

router.delete(
  '/:id',
  auth.check,
  auth.simpleCheckRoles(['superAdmin', 'admin']),
  generalValidator(validator.remove),
  controller.remove
)

export default router
>>>>>>> origin/armanriazi
