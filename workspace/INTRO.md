**Notices**

## Package.json

During the initial package installation, be sure to use `npm` to carry out the installation process and strictly avoid using pnpm, as it will lead to several errors. However, it is possible that after installing with npm, you can execute the work and run scripts using pnpm. Therefore, in this section, several suitable commands have been provided for use, especially one of the most important ones is `pnpm:clean`.

```bash
    "pnpm:version": "pnpm --version",
    "pnpm:yourpackage": "pnpm view yourpackage versions",
    "pnpm:clean": "find ./ -name node_modules -type d -exec rm -rf {} +",
    "pnpm:update": "pnpm -r update -i -L",
    "pnpm:dedupe": "pnpm dedupe --check",
    "pnpm:check": "pnpm run lint:md && pnpm run lint:fix && pnpm run compile",
    "pnpm:dev": "nodemon src/index.js",
    "deps": "pnpm update -i --latest -r",
    "pnpm:start": "pnpm run build:live",
    "debug": "DEBUG=http,express:*,app:* nodemon index.js",
    "node:dev": "cross-env NODE_ENV=development node -r esm ./src/index",
    "node:inspect": "cross-env NODE_ENV=development && cross-env NODE_INSPECT_RESUME_ON_START=1 node inspect ./src/modules/users/users.controller.js",
    "node:inspect:9223": "cross-env NODE_ENV=development && cross-env NODE_INSPECT_RESUME_ON_START=1 node --inspect=9223 ./src/modules/users/users.controller.js"    
```

## Docker

Occocionally, To set the correct time for the time_log variable in Docker, which sometimes can be problematic, you can use the following command inside your Dockerfile:

`RUN echo "date '+%Y-%m-%d %H:%M:%S'" > /etc/timezone`

This command will set the system's timezone to match the local timezone, which should help with populating the time_log variable correctly. Remember to replace the timezone format and value according to your specific needs.

> `ENV TZ="Asia/Tehran"`


## ExpressJS

The two main objects, namely request and response, undergo changes throughout all sections of the program. Therefore, paying attention to the components of this section can be helpful to you.

### Request

Includes:

```json
{
    "req" :  {
        "body": {
            "type": {},
            "userId": {},
            "occlusion": {},
        },
        "headers": {},
        "originalUrl": {},
        "method" : {
            "get": {},
            "post": {},
        },        
        "requestType" : {},
        "srtProxy" : {},        
        "projectName": {},
        "accessAiService": {
            "Service" : {
                "host": {},
                "protocol": {},
                "rejectUnauthorized": {},
            }
        },
        "serviceName" : {},
        "path": {},
        "hasFile" : {},
        "filePath" : {},
        "file": {
            "howGet" : {},
        },
        "files": {},
        "ip": {},
        "query": {},
        "params": {},
        "user": {},        
        "clause": {},
        "isAllowedIP" : {},
        "gatewayLogData":{},
    }
}
```

### Response

```json
{
    "res": {
        "locals": {},
        "requestId" : {},
        "sendFile": {},
        "json": {},
        "data": {
            "status":{},
            "message": {
                "data" : {}
            }
       },
    }
}
```
