
```javascript



/** @module Logger */
import Logger from "partbackendlogger";
import "dotenv/config";

const logger = new Logger({
  appName: process.env.LOGGER_SERVICE,
  appVersion: process.env.PROJECT_VERSION,
  environment: process.env.LOGGER_ENV,
  url: process.env.LOGGER_URL,
  timeout: process.env.LOGGER_TIMEOUT,
});

export default logger;
```