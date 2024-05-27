### Function names

- [x] isRoleExists
- [x] isDbExists
- [x] createDb --> isDbExists
- [x] createRole --> isRoleExists

‍‍‍```javascript
import "dotenv/config";
import Sequelize, { QueryTypes } from "sequelize";
import config from "../src/config/database.js";
import logger from "../src/services/logger.js";

const env = process.env.NODE_ENV || "development";
const envConfig = config[env];
/**

- @description Checks if the role in database exists or not
- @function isRoleExists
- @param {object} sequelize
- @returns boolean
 */
const isRoleExists = async (sequelize) => {
  const result = await sequelize.query(
    `SELECT 1 FROM pg_roles WHERE rolname = '${envConfig.username}'`,
    { type: QueryTypes.SELECT }
  );
  return !!result.length;
};

/**

- @description Checks if the database exists or not
- @function isDbExists
- @param {object} sequelize
- @returns boolean
 */
const isDbExists = async (sequelize) => {
  const result = await sequelize.query(
    `SELECT 1 FROM pg_catalog.pg_database WHERE datname = '${envConfig.database}'`,
    { type: QueryTypes.SELECT }
  );
  return !!result.length;
};

/**

- @description create database
- @function createDb
- @param {object} sequelize
- @returns void
 */
const createDb = async (sequelize) => {
  if (await isDbExists(sequelize)) {
    console.log("database is created before");
    return;
  }
  console.log(`CREATE DATABASE ${envConfig.database}`);
  await sequelize.query(`CREATE DATABASE ${envConfig.database}`);
};

/**

- @description create role in database
- @function createRole
- @param {object} sequelize
- @returns void
 */
const createRole = async (sequelize) => {
  if (await isRoleExists(sequelize)) {
    await sequelize.query(`ALTER ROLE ${envConfig.username} WITH SUPERUSER`);
    console.log("role is created before");
    return;
  }
  console.log(
    `CREATE ROLE ${envConfig.username} WITH LOGIN PASSWORD '${envConfig.password}'`
  );
  console.log(`ALTER ROLE ${envConfig.username} WITH SUPERUSER`);
  await sequelize.query(
    `CREATE ROLE ${envConfig.username} WITH LOGIN PASSWORD '${envConfig.password}'`
  );
  await sequelize.query(`ALTER ROLE ${envConfig.username} WITH SUPERUSER`);
};

/**

- @description init database
- @function createDatabase
 */
(async () => {
  try {
    const sequelize = new Sequelize(
      process.env.PG_DATABASE,
      process.env.PG_USER,
      process.env.PG_PASS,
      envConfig
    );
    await createRole(sequelize);
    await createDb(sequelize);
    await sequelize.close();
    process.exit(0);
  } catch (error) {
    console.log({ error });
    logger.log({
      level: "error",
      message: {
        message: error.message,
        stack: error.stack,
        type: "database",
      },
    });
    process.exit(1);
  }
})();
```
