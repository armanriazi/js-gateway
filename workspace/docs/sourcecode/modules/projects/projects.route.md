
import { Router } from "express";
import generalValidator from "../../common/middlewares/generalValidator.js";
import controller from "./projects.controller.js";
import validator from "./projects.validator.js";
import auth from "../../services/auth.js";
import paginationHandler from "../../common/middlewares/paginationHandler.js";
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
  controller.getProject
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
import controller from './projects.controller'
import validator from './projects.validator'
import auth from '../../services/auth'
import paginationHandler from '../../common/middlewares/paginationHandler'
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
  controller.getProject
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
