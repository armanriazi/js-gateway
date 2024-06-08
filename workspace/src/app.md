

```javascript
import fs from "fs";
import https from "https";
import http from "http";
import express from "express";
import error from "http-errors";
import cors from "cors";
import Broker from "./services/broker.js";
import routers from "./modules/index.js";
import publicMiddlewares from "./common/middlewares/public.js";
import errorHandler from "./common/middlewares/errorHandler.js";
import { saveResultLog, createLog } from "./common/middlewares/storeLog.js";
```

Using Express.js

Type: web framework


‍‍‍‍‍‍‍```javascript
‍‍‍const app = express();
```

Type: External library


```javascript
global.error = error;
```

To prevent common errors related to cross-origin, we use the following code.

-[x] "Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at $somesite"
-[x] "Reason: CORS header 'Access-Control-Allow-Origin' missing"

Type: External library

```javascript
app.use(cors());
```

To log user requests in the gateway, the request must be directed to the gatewayLogsService. Additionally, the checkServerHealth request should be checked upon entry to ensure that all the checkpoints are present in the storeLog code file.
Type: Middleware


```javascript
app.use(createLog);
```

Type: Middleware

```javascript
app.use(saveResultLog);
```

To have a public address as the main address when a web application is loading, the introduced middleware is used. For example, we can add 'download' as the main address, not an API. router.use("/download")" 
Type: Middleware

```javascript
app.use(publicMiddlewares);
```


Routers are used from the module path that exists in the project.

```javascript
app.use(routers);
```

An error handler is used to detect and obtain the errors that have occurred in the application controllers. The partbackendlogger library collection is utilized. For more information, please refer to the log/audit processes section.
Type: Middleware


```javascript
app.use(errorHandler);
```

Rabbitmq, create a new instance

```javascript
Broker.getInstance();
```

The default path process.cwd refers to the gateway folder or the main branch of the application. Check the upload path and attach the necessary certificates to the program if they exist, or use the HTTPS protocol.

```javascript
if (!fs.existsSync(`${process.cwd()}/uploads`)) {
  fs.mkdirSync(`${process.cwd()}/uploads`);
}

export const credentials = fs.existsSync("certs/selfsigned.key") &&
  fs.existsSync("certs/selfsigned.crt") && {
    key: fs.readFileSync("certs/selfsigned.key"),
    cert: fs.readFileSync("certs/selfsigned.crt"),
  };

export const server = credentials
  ? https.createServer(credentials, app)
  : http.createServer(app);
```
