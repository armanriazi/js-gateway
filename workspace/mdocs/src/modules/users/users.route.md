
import { Router } from "express";
import generalValidator from "../../common/middlewares/generalValidator.js";
import paginationHandler from "../../common/middlewares/paginationHandler.js";
import controller from "./users.controller.js";
import validator from "./users.validator.js";
import auth from "../../services/auth.js";
const router = Router({ mergeParams: true });

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
  auth.simpleCheckRoles(["superAdmin", "admin", "user"]),

import { Router } from 'express'
import generalValidator from '../../common/middlewares/generalValidator'
import paginationHandler from '../../common/middlewares/paginationHandler'
import controller from './users.controller'
import validator from './users.validator'
import auth from '../../services/auth'
const router = Router({ mergeParams: true })

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
  auth.simpleCheckRoles(['superAdmin', 'admin', 'user']),
>>>>>>> origin/armanriazi
  auth.checkIdInParamsWithReqUserId,
  auth.checkAdminCanNotGetAdmins,
  generalValidator(validator.get),
  controller.getUser

);

router.post("/login", generalValidator(validator.login), controller.login);

router.post(
  "/",
  auth.check,
  auth.simpleCheckRoles(["superAdmin", "admin"]),
  auth.checkRequestRole,
  generalValidator(validator.create),
  controller.create
);

router.patch(
  "/password/:id",
  auth.check,
  auth.simpleCheckRoles(["superAdmin", "admin", "user"]),
  auth.checkIdInParamsWithReqUserId,
  generalValidator(validator.updatePassword),
  controller.updatePassword
);

router.patch(
  "/info/:id",
  auth.check,
  auth.simpleCheckRoles(["superAdmin", "admin", "user"]),
  auth.checkIdInParamsWithReqUserId,
  auth.checkRoleIsAllowed("role"),
  generalValidator(validator.update),
  controller.updateUser
);

router.delete(
  "/:id",
  auth.check,
  auth.simpleCheckRoles(["superAdmin", "admin"]),
  generalValidator(validator.remove),
  controller.removeUser
);

export default router;

)

router.post('/login', generalValidator(validator.login), controller.login)

router.post(
  '/',
  auth.check,
  auth.simpleCheckRoles(['superAdmin', 'admin']),
  auth.checkRequestRole,
  generalValidator(validator.create),
  controller.create
)

router.patch(
  '/password/:id',
  auth.check,
  auth.simpleCheckRoles(['superAdmin', 'admin', 'user']),
  auth.checkIdInParamsWithReqUserId,
  generalValidator(validator.updatePassword),
  controller.updatePassword
)

router.patch(
  '/info/:id',
  auth.check,
  auth.simpleCheckRoles(['superAdmin', 'admin', 'user']),
  auth.checkIdInParamsWithReqUserId,
  auth.checkRoleIsAllowed('role'),
  generalValidator(validator.update),
  controller.updateUser
)

router.delete(
  '/:id',
  auth.check,
  auth.simpleCheckRoles(['superAdmin', 'admin']),
  generalValidator(validator.remove),
  controller.removeUser
)

export default router
>>>>>>> origin/armanriazi
