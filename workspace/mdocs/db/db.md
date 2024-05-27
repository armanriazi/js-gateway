The ORM type for database connectivity is Sequelize. To work with it, you need to consider three parts of the project:

1. The .sequelizerc file
2. The db folder
3. The existing commands for working with it in the package.json section

Since we have the option to use the database in a code-first or model-based manner, there is a migration settings section, and you can activate this section based on your environment and use this feature.

```bash
#/bin/bash
pnpm run db:init
pnpm run db:migrate
pnpm run db:run:seed
pnpm start
```

To create a database, code-first is used by line 1.
The second line performs the migration or conversion of code into relational tables in the actual database.
The third line can be used to initialize and seed several tuples of data in the table.
The last command is to start the program.

```bash
    "db:init": "node ./db/init",
    "db:drop": "node ./db/drop",
    "db:init:test": "cross-env NODE_ENV=test node ./db/initDatabase",
    "db:migrate": "npx sequelize-cli  db:migrate",
    "db:migrate:test": "cross-env NODE_ENV=test npx sequelize-cli  db:migrate",
    "db:migrate:generate": "npx sequelize-cli  migration:generate --name",
    "db:migrate:undo": "npx sequelize-cli  db:migrate:undo",
    "db:create:seed": "npx sequelize-cli  seed:generate --name",
    "db:run:seed": "npx sequelize-cli  db:seed:all",
    "db:undo:seed": "npx sequelize-cli  db:seed:undo",
    "seq:init": "npx sequelize-cli  init --force",
    "db:migrate:create": "pnpm sequelize-cli migration:generate",
    "db:reset:dev": "cross-env NODE_ENV=development npm run db:drop  &&  cross-env NODE_ENV=development npm run db:init  && cross-env NODE_ENV=development npm run db:migrate && cross-env NODE_ENV=development npm run db:run:seed",
    "db:reset:test": "cross-env NODE_ENV=test npm run db:drop  &&  cross-env NODE_ENV=test npm run db:init  && cross-env NODE_ENV=test npm run db:migrate && cross-env NODE_ENV=test npm run db:run:seed",
    "test": "cross-env NODE_ENV=test npm run db:reset:test  && pnpm jest --clearCache  && cross-env NODE_ENV=test
    "user_factory": "node ./db/factory/apiLogFactory.js",
```
