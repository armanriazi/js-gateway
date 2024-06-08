import Sequelize from "sequelize";
import env from "../common/env.js";
import databaseConfig from "../config/database.js";
import User from "./users/users.model.js";
import ApiKey from "./apiKeys/apiKeys.model.js";
import GatewayLog from "./gatewayLogs/gatewayLogs.model.js";
import Service from "./services/services.model.js";
import Project from "./projects/projects.model.js";
import Apis from "./apis/apis.model.js";
// const config = databaseConfig[env("NODE_ENV")];
const db = {};
const models = [User, GatewayLog, ApiKey, Project, Apis, Service, Service];
let sequelize;

// if (env('NODE_ENV') === 'production') {
//   const context = require
//     .context('/src/modules/', true, /model.js$/, 'sync')
//     .keys()
//     .map(context)
//     .forEach((module) => {
//       const sequelizeModel = module.default(sequelize, Sequelize)

//       db[sequelizeModel.name] = sequelizeModel
//     })
// } else {
//   fs.readdirSync(path.join(process.cwd(), 'src/modules/')).forEach((module) => {
//     if (module !== 'index.js' && module !== 'models.js') {
//       fs.readdirSync(path.join(process.cwd(), 'src/modules/', module)).forEach(
//         (file) => {
//           if (file.includes('model.js')) {
//             const sequelizeModel = require(path.join(
//               process.cwd(),
//               'src/modules/',
//               module,
//               file
//             )).default(sequelize, Sequelize.DataTypes)
//             db[sequelizeModel.name] = sequelizeModel
//           }
//         }
//       )
//     }
//   })
// }

// ----------------------------
class Models {
  constructor() {
    this.env = env("NODE_ENV") || "development";
    this.config = databaseConfig[this.env];
    this.db = {
      models: {},
    };
    this.sequelize = null;
    if (this.config.use_env_variable) {
      this.sequelize = new Sequelize(
        process.env[this.config.use_env_variable],
        this.config
      );
    } else {
      this.sequelize = new Sequelize(
        this.config.database,
        this.config.username,
        this.config.password,
        this.config
      );
    }

    this.db.sequelize = this.sequelize;
    this.db.Sequelize = Sequelize;
    this.generateModels();
    this.associations();
  }

  generateModels() {
    for (const model of models) {
      const modelClass = model(this.sequelize, Sequelize.DataTypes);
      this.db[modelClass.name] = modelClass;
    }
  }

  associations() {
    Object.keys(this.db).forEach((modelName) => {
      if (this.db[modelName].associate) {
        this.db[modelName].associate(this.db);
      }
    });
  }
}
const modelObj = new Models();
db.models = modelObj.db;
// ----------------------------

db.sequelize = sequelize;
db.Sequelize = Sequelize;
export default db;
