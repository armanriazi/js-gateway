
```javascript



import { Router } from "express";
import ajv from "../../common/middlewares/ajvValidator.js";
import controller from "./aiServices.controller.js";
import {
  // checkApi,
  saveFile,
  checkIp,
  checkProxyApi,
} from "./aiServices.service.js";
import busboy from "../../services/busboyUploader.js";
import check from "../../common/middlewares/policy.js";

const router = Router({ mergeParams: true });

router.get("*", checkIp, checkProxyApi, controller.get);

router.all(
  "*",
  checkIp,
  checkProxyApi,
  busboy.upload,
  saveFile,
  ajv.ajvValidator,
  check.policy,
  controller.proxyOrDirect
);

// router.get('/pr-*', checkIp, checkProxyApi, controller.proxyToServices)

// router.post(
//   '*',
//   checkIp,
//   checkApi,
//   busboy.upload,
//   saveFile,
//   ajv.ajvValidator,
//   check.policy,
//   controller.create,
// )

// router.get('*', checkIp, checkProxyApi, controller.get)

export default router;
```