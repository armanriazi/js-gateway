# Middleware Logger

The initial settings pertain to the placement of middlewares, which must be arranged in a specific order.

```javascript
app.use(createLog)//...

app.use(saveResultLog)

app.use(publicMiddlewares)

app.use(routers)//...
```

## Winston Loger

One of the uses of this library in the gateway application is to write logs in the error.log file, which is located in the logs folder path.

For example, the content of this file can include the creation of logs for the following errors:


```md
 **1403/2/25, 11:55:28 [error]:**  listen EADDRINUSE: address already in use :::3002 "Error: listen EADDRINUSE: address already in use :::3002\n    at Server.setupListenHandle [as _listen2] (node:net:1817:16)\n    at listenInCluster (node:net:1865:12)\n    at Server.listen (node:net:1953:7)\n    at Object.<anonymous> (/home/armanriazi/partdp-projects/gateway/gateway/src/index.js:9:8)\n    at Generator.next (<anonymous>)\n    at bl (/home/armanriazi/partdp-projects/gateway/node_modules/esm/esm.js:1)\n    at kl (/home/armanriazi/partdp-projects/gateway/node_modules/esm/esm.js:1)\n    at Object.u (/home/armanriazi/partdp-projects/gateway/node_modules/esm/esm.js:1)\n    at Object.o (/home/armanriazi/partdp-projects/gateway/node_modules/esm/esm.js:1)\n    at Object.<anonymous> (/home/armanriazi/partdp-projects/gateway/node_modules/esm/esm.js:1)"
**1403/2/25, 13:15:18 [error]:**  ERROR OCURRED ON BROKER CONNECTION : Error: Unexpected close
    at succeed (/home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:280:13)
    at onOpenOk (/home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:262:5)
    at /home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:165:32
    at /home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:159:12
    at Socket.recv (/home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:507:12)
    at Object.onceWrapper (node:events:631:28)
    at Socket.emit (node:events:517:28)
    at Socket.emit (node:domain:489:12)
    at emitReadable_ (node:internal/streams/readable:633:12)
    at process.processTicksAndRejections (node:internal/process/task_queues:81:21) "Error: ERROR OCURRED ON BROKER CONNECTION : Error: Unexpected close\n    at succeed (/home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:280:13)\n    at onOpenOk (/home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:262:5)\n    at /home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:165:32\n    at /home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:159:12\n    at Socket.recv (/home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:507:12)\n    at Object.onceWrapper (node:events:631:28)\n    at Socket.emit (node:events:517:28)\n    at Socket.emit (node:domain:489:12)\n    at emitReadable_ (node:internal/streams/readable:633:12)\n    at process.processTicksAndRejections (node:internal/process/task_queues:81:21)\n    at ChannelModel.<anonymous> (/home/armanriazi/partdp-projects/gateway/gateway/src/services/broker.js:47:15)\n    at ChannelModel.emit (node:events:517:28)\n    at ChannelModel.emit (node:domain:489:12)\n    at Connection.emit (node:events:517:28)\n    at Connection.emit (node:domain:489:12)\n    at C.onSocketError (/home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:361:10)\n    at Socket.emit (node:events:529:35)\n    at Socket.emit (node:domain:489:12)\n    at endReadableNT (node:internal/streams/readable:1400:12)\n    at process.processTicksAndRejections (node:internal/process/task_queues:82:21)"
```

## Save logs to the DB

Depending on whether the request is an API or a service, the type of storage is determined. Always keep in mind that when debugging, the order of execution may not be clear due to the asynchronous nature of the functions. However, regarding the storage of requests and responses in the GatewayLogs table, consider...

## Using lib of partbackendlogger

This library is connected to an endpoint, the settings of which are done in the .env file. Therefore, with the creation of each log, you can send it to the specified server and then view its information in Grafana. For other settings, you can use the backendLogger file in the services section.


## Logger service file

This file utilizes the partbackendlogger library. Or it can be said to be the complement of this service in our program. Therefore, this file is the final part of the log, and the Winston library will only be used here throughout the project.

## How to use


 ```javascript
 import logger from './backendLogger'

export default async ({ req, res, responseBody, errStack }) => {
  try {
    if (errStack) {
      // send to logger service
      logger.errorLog({ res, error: errStack })
    }
  }
  catch{}
 ```

## Log Json Structure Object

```json
{
    "log" : {
       "requestId":{},
        "meta": {
          "userAgent": {},
          "ip": {},
          "method": {},
          "originalUrl": {},
          "path": {},
        },
        "data": {
          "status" : {},
          "message" : {},
          "request" : {},
          "response" : {},
        },
    }
}
```
