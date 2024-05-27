

```javascript




import { Model } from "sequelize";

export default (sequelize, DataTypes) => {
  class Api extends Model {
    static associate(models) {
      Api.belongsTo(models.ApiKey, {
        foreignKey: "ApiKeyId",
        targetKey: "id",
      });

      Api.belongsTo(models.Service, {
        foreignKey: "ServiceId",
        targetKey: "id",
      });
    }
  }

  Api.init(
    {
      id: {
        allowNull: false,
        autoIncrement: false,
        primaryKey: true,
        type: DataTypes.UUID,
        defaultValue: DataTypes.UUIDV4,
      },
      ApiKeyId: {
        type: DataTypes.UUID,
        allowNull: false,
        onUpdate: "CASCADE",
        onDelete: "CASCADE",
        field: "api_key_id",
        references: {
          model: "ApiKey",
          key: "id",
        },
      },
      ServiceId: {
        type: DataTypes.UUID,
        allowNull: false,
        onUpdate: "CASCADE",
        onDelete: "CASCADE",
        field: "service_id",
        references: {
          model: "Service",
          key: "id",
        },
      },
      apis: DataTypes.JSONB,
      TPM: {
        type: DataTypes.INTEGER,
        field: "tpm",
        allowNull: false,
        defaultValue: -1,
      },
      maxCount: {
        type: DataTypes.INTEGER,
        allowNull: false,
        field: "max_count",
        defaultValue: -1,
      },
      policies: {
        type: DataTypes.JSONB,
        allowNull: true,
      },
      createdAt: {
        type: DataTypes.DATE,
        field: "created_at",
        allowNull: false,
      },
      updatedAt: {
        type: DataTypes.DATE,
        field: "updated_at",
        allowNull: false,
      },
    },
    {
      sequelize,
      paranoid: false,
      modelName: "Api",
    }
  );
  return Api;
};
```