

```javascript




import { Model } from "sequelize";

export default (sequelize, DataTypes) => {
  class ApiKey extends Model {
    static associate(models) {
      ApiKey.belongsTo(models.User, {
        foreignKey: "UserId",
        targetKey: "id",
      });

      ApiKey.hasMany(models.Api);
    }
  }

  ApiKey.init(
    {
      id: {
        allowNull: false,
        autoIncrement: false,
        primaryKey: true,
        type: DataTypes.UUID,
        defaultValue: DataTypes.UUIDV4,
      },
      value: {
        type: DataTypes.STRING,
        allowNull: false,
      },
      UserId: {
        type: DataTypes.UUID,
        allowNull: false,
        onUpdate: "CASCADE",
        onDelete: "CASCADE",
        field: "user_id",
        references: {
          model: "User",
          key: "id",
        },
      },
      isValid: {
        type: DataTypes.BOOLEAN,
        allowNull: false,
        defaultValue: true,
        field: "is_valid",
      },
      maxCount: {
        type: DataTypes.INTEGER,
        allowNull: false,
        field: "max_count",
        defaultValue: -1,
      },
      expireDate: {
        type: DataTypes.DATE,
        field: "expire_date",
        allowNull: false,
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
      modelName: "ApiKey",
    }
  );
  return ApiKey;
};
```