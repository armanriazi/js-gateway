
```javascript



import gatewayLogsService from "../../modules/gatewayLogs/gatewayLogs.service.js";
import path from "path";

/**
 * check api is valid in method GET if is not valid return not found or access denied
 * @type {function}
 * @function createLog
 * @param {object} req - express request object
 * @param {object} res - express response object
 * @param {function} next - function that sends request to the next middleware
 */
export const createLog = async (req, res, next) => {
  try {
    const { headers, method, originalUrl, ip } = req;
    const gatewayService = originalUrl.split(path.sep)[1];
    const request = {
      originalUrl,
      method,
      headers,
      ip,
      logType: "gateway",
    };

    if (!originalUrl.includes("checkServerHealth")) {
      const token = req.get("Authorization");
      let uniqueKey = token && token.replace("Bearer ", "");

      if (gatewayService === "service") {
        const apiKey = req.get("ApiKey");
        uniqueKey = apiKey;
        request.logType = "ai";
      }

      const gatewayLog = await gatewayLogsService.create({
        uniqueKey,
        data: [{ request }],
      });
      gatewayLog && (req.gatewayLogId = gatewayLog.id);
      gatewayLog && (req.gatewayLogData = gatewayLog.data);
    }
    next();
  } catch (err) {
    next(err);
  }
};

export const saveResultLog = (req, res, next) => {
  const old = res.send.bind(res);
  res.send = async (body) => {
    const response = JSON.parse(body);
    if (!req.originalUrl.includes("checkServerHealth")) {
      const { request } = req.gatewayLogData[0];
      request.body = { ...req.body };
      request.params = { ...req.params };
      request.query = { ...req.query };
      req.gatewayLogData.push({ response: response.data });
      await gatewayLogsService.updateOne(
        {
          data: req.gatewayLogData,
          status: response.data.status,
        },
        { id: req.gatewayLogId }
      );
      const meta = {
        requestId: req.gatewayLogId,
      };

      response.meta = meta;
    }
    old(JSON.stringify(response));
  };
  next();
};
```