
"use strict";

import bcrypt from "bcryptjs";

import { Model } from "sequelize";
const { genSaltSync, hashSync } = bcrypt;

'use strict'

import { genSaltSync, hashSync } from 'bcryptjs'
import { Model } from 'sequelize'


export default (sequelize, DataTypes) => {
  class User extends Model {
    toJSON() {

      const attributes = this.get();

      delete attributes.password;

      return attributes;
    }

    static associate(models) {
      User.hasMany(models.ApiKey);

      const attributes = this.get()

      delete attributes.password

      return attributes
    }

    static associate(models) {
      User.hasMany(models.ApiKey)

    }
  }

  User.init(
    {
      id: {
        allowNull: false,
        autoIncrement: false,
        primaryKey: true,
        type: DataTypes.UUID,
        defaultValue: DataTypes.UUIDV4,
      },
      username: {
        type: DataTypes.STRING,

        field: "username",

        field: 'username',

        unique: true,
      },
      password: {
        type: DataTypes.STRING,
        set(value) {

          this.setDataValue("password", hashSync(value, genSaltSync(12)));

          this.setDataValue('password', hashSync(value, genSaltSync(12)))

        },
      },
      role: {
        type: DataTypes.STRING,
        allowNull: false,
      },
      profile: {
        type: DataTypes.JSONB,
        allowNull: false,
        defaultValue: {},
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
      deletedAt: {
        type: DataTypes.DATE,

        field: "deleted_at",

        field: 'deleted_at',

      },
    },
    {
      sequelize,
      paranoid: true,

      modelName: "User.",
    }
  );
  return User;
};

      modelName: 'User',
    }
  )
  return User
}

