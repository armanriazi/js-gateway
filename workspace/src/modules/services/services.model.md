
"use strict";

import { Model } from "sequelize";

'use strict'

import { Model } from 'sequelize'


export default (sequelize, DataTypes) => {
  class Service extends Model {
    static associate(models) {
      Service.belongsTo(models.Project, {

        foreignKey: "ProjectId",
        targetKey: "id",
      });

      Service.hasMany(models.Api);

        foreignKey: 'ProjectId',
        targetKey: 'id',
      })

      Service.hasMany(models.Api)

    }
  }

  Service.init(
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
      ProjectId: {
        type: DataTypes.UUID,
        allowNull: false,

        onUpdate: "CASCADE",
        onDelete: "CASCADE",
        field: "project_id",
        references: {
          model: "Project",
          key: "id",

        onUpdate: 'CASCADE',
        onDelete: 'CASCADE',
        field: 'project_id',
        references: {
          model: 'Project',
          key: 'id',

        },
      },
      validation: DataTypes.JSONB,
      apis: DataTypes.JSONB,
      queue: {
        type: DataTypes.STRING,
        allowNull: false,
      },
      type: {
        type: DataTypes.STRING,
        allowNull: true,
      },
      host: {
        type: DataTypes.STRING,
        allowNull: true,
      },
      protocol: {
        type: DataTypes.STRING,
        allowNull: true,
      },
      rejectUnauthorized: {
        type: DataTypes.BOOLEAN,
        allowNull: false,
        defaultValue: false,
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

      modelName: "Service",
      indexes: [
        {
          unique: true,
          fields: ["name"],
        },
      ],
    }
  );
  return Service;
};

      modelName: 'Service',
      indexes: [
        {
          unique: true,
          fields: ['name'],
        },
      ],
    }
  )
  return Service
}

