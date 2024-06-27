
"use strict";

import { Model } from "sequelize";

'use strict'

import { Model } from 'sequelize'


export default (sequelize, DataTypes) => {
  class Project extends Model {
    static associate(models) {

      Project.hasMany(models.Service);

      Project.hasMany(models.Service)

    }
  }

  Project.init(
    {
      id: {
        allowNull: false,
        autoIncrement: false,
        primaryKey: true,
        type: DataTypes.UUID,
        defaultValue: DataTypes.UUIDV4,
      },
      name: {
        type: DataTypes.STRING,

        field: "name",

        field: 'name',

        unique: true,
      },
      createdAt: {
        type: DataTypes.DATE,

        field: "created_at",

        field: 'created_at',

        allowNull: false,
      },
      updatedAt: {
        type: DataTypes.DATE,

        field: "updated_at",

        field: 'updated_at',

        allowNull: false,
      },
    },
    {
      sequelize,
      paranoid: false,

      modelName: "Project",
    }
  );
  return Project;
};

      modelName: 'Project',
    }
  )
  return Project
}

