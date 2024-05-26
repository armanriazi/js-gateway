|   Name   | Type | Description |
| :------: | :--: | :---------: |
| database | env  | configured  |
| username | env  | configured  |
| password | env  | configured  |


```javascript



import Sequelize, { QueryTypes } from "sequelize";
import config from "../src/config/database.js";
import env from "../src/common/env.js";

const runTimeEnv = process.env.NODE_ENV || "development";
const envConfig = config[runTimeEnv];
/**
 * @function dropDatabase
 * @description drop an exist database based on the config of `env`
 * @param {object} database
 * @param {object} username
 * @param {object} password
 * @public
 */
export const dropDatabase = async () => {
  const dropDb = `DROP DATABASE ${envConfig.database}`;
  const dropRole = `DROP ROLE ${envConfig.username}`;
  try {
    const sequelize = new Sequelize(
      env("PG_DATABASE"),
      env("PG_USER"),
      env("PG_PASS"),
      envConfig
    );
    const existDb = await sequelize.query(
      `SELECT 1 FROM pg_catalog.pg_database WHERE datname = '${envConfig.database}';`,
      { type: QueryTypes.SELECT }
    );
    const existRole = await sequelize.query(
      `SELECT 1 FROM pg_roles WHERE rolname = '${envConfig.username}';`,
      { type: QueryTypes.SELECT }
    );

    existDb.length && (await sequelize.query(dropDb));

    existRole.length && (await sequelize.query(dropRole));

    await sequelize.close();

    process.exit(0);
  } catch (err) {
    // eslint-disable-next-line no-console
    console.log("ERROR IN DROPPING DATABASE", err);
  }
};
```
