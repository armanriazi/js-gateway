import { Router } from "express";
import usersRouter from "./users/users.route.js";
import apiKeysRouter from "./apiKeys/apiKeys.route.js";
import projectsRouter from "./projects/projects.route.js";
import servicesRouter from "./services/services.route.js";
import apisRouter from "./apis/apis.route.js";
import gatewayLogsRouter from "./gatewayLogs/gatewayLogs.route.js";
import messages from "../common/messages.js";
import aiServicesRouter from "./aiServices/aiServices.route.js";
import responseMessage from "../common/responseMessage.js";
import logger from "../services/backendLogger.js";

const router = Router({ mergeParams: true });

router.use("/service", logger.requestLog, aiServicesRouter);
router.use("/api/users", usersRouter);
router.use("/api/apikeys", apiKeysRouter);
router.use("/api/projects", projectsRouter);
router.use("/api/services", servicesRouter);
router.use("/api/apis", apisRouter);
router.use("/api/gatewaylogs", gatewayLogsRouter);
router.get("/api/checkServerHealth", (req, res) => {
  responseMessage({ res, statusCode: 200, data: "ok" });
});

router.use("*", () => {
  throw global.error(404, messages.m.NOT_FOUND_ROUTE_ERROR);
});

export default router;
