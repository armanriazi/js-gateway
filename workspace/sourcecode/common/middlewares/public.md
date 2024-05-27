
```javascript



import express from "express";
import compression from "compression";
import helmet from "helmet";
import env from "../env.js";

const router = express.Router();

if (env("NODE_ENV") === "development") {
  router.use(require("morgan")("combined"));
}

router.use(express.json({ limit: "50mb" }));

router.use(express.urlencoded({ extended: true }));

if (["test", "production"].includes(env("NODE_ENV"))) {
  router.use(compression());
  router.use(helmet());
}

router.use("/download", express.static("outgoing"));

export default router;
```