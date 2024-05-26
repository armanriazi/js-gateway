
```javascript



import Redis from "ioredis";
import chalk from "chalk";
import env from "../common/env.js";
import messages from "../common/messages.js";

const redisConnection = new Redis({
  port: Number(env("REDIS_PORT")),
  host: env("REDIS_HOST"),
  db: Number(env("REDIS_DB")),
  password: env("REDIS_PASS"),
});

console.log(
  chalk.bold.cyan(`
        ----------------------------
              REDIS CONNECTED
        ----------------------------`)
);
redisConnection.connect(() => console.log(messages.REDIS_LOG));
export default redisConnection;
```