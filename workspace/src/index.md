The function `assertDatabaseConnectionOk` does not exist in the main code, but it can be added to check the database connection.

```javascript
import chalk from 'chalk';
import env from "./common/env.js";
import winston from "./common/helper/winston.js";
import { server, credentials } from "./app.js";
import cluster from 'cluster';
import { grpcHandler } from "./grpc/grpcHandler.js";
import { webSocketHandler } from "./websocket/websocketHandler.js";
import sequelize from 'sequelize';

async function assertDatabaseConnectionOk() {
   winston.logger.info(`Checking database connection...`);
  try {
    await sequelize.authenticate();
    console.log(
      chalk.bold.cyan(`
        ----------------------------
            POSTGRES CONNECTED
        ----------------------------`),
    );
  } catch (error) {
    winston.error("Unable to connect to the database:", error.stack);
    process.exit(1);
  }
}

assertDatabaseConnectionOk();

server.listen(env('PORT'), () => {
  console.log(
    chalk.bold.cyan(`
    ${
      credentials ? 'HTTPS SERVER' : 'HTTP SERVER'
    } IS UP AND RUNNING ON PORT ${env('PORT')} 🚀
    --------------------------------------------
    RUNNING MODE IS ${env('NODE_ENV')}`)
  )
})


webSocketHandler()
  console.log(
    chalk.bold.cyan(`\n    WEBSOCKET SERVER RUNNING ON PORT 8080 🚀 `)
  )

grpcHandler()
  console.log(
    chalk.bold.cyan(`\n    GRPC SERVER RUNNING ON PORT 3020 🚀 `)
  )


process
  .on('unhandledRejection', (reason, p) => {
    winston.error(`Unhandled Rejection at Promise : ${reason}`, p)
  })
  .on('uncaughtException', (err) => {
    winston.error(err.message, err.stack)
  })

// if (cluster.isMaster) {
//   for (let i = 0; i < +(env("CPUS")); i++) {
//     cluster.fork()
//     console.log(`worker process id : ${process.pid} `)
//   }

//   cluster.on('exit', (worker, code, signal) => {
//     console.log(`worker ${worker.process.pid} died`)
//     cluster.fork()
//   })
// } else {
// server.listen(env('PORT'), () => {
//   console.log(
//     chalk.bold.cyan(`
//     ${
//       credentials ? 'HTTPS SERVER' : 'HTTP SERVER'
//     } IS UP AND RUNNING ON PORT ${env('PORT')} 🚀
//     --------------------------------------------
//     RUNNING MODE IS ${env('NODE_ENV')}`)
//   )
// })

//   process
//     .on('unhandledRejection', (reason, p) => {
//       winston.error(`Unhandled Rejection at Promise : ${reason}`, p)
//     })
//     .on('uncaughtException', (err) => {
//       winston.error(err.message, err.stack)
//     })
// }
```
