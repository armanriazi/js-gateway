
"use strict";

import { Model } from "sequelize";

'use strict'

import { Model } from 'sequelize'
>>>>>>> origin/armanriazi

export default (sequelize, DataTypes) => {
  class Project extends Model {
    static associate(models) {

      Project.hasMany(models.Service);

      Project.hasMany(models.Service)
>>>>>>> origin/armanriazi
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
>>>>>>> origin/armanriazi
        unique: true,
      },
      createdAt: {
        type: DataTypes.DATE,

        field: "created_at",

        field: 'created_at',
>>>>>>> origin/armanriazi
        allowNull: false,
      },
      updatedAt: {
        type: DataTypes.DATE,

        field: "updated_at",

        field: 'updated_at',
>>>>>>> origin/armanriazi
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
>>>>>>> origin/armanriazi
