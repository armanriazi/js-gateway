
```javascript




import { Model } from "sequelize";

export default (sequelize, DataTypes) => {
  class GatewayLog extends Model {}

  GatewayLog.init(
    {
      id: {
        allowNull: false,
        autoIncrement: false,
        primaryKey: true,
        type: DataTypes.UUID,
        defaultValue: DataTypes.UUIDV4,
      },
      uniqueKey: {
        allowNull: true,
        field: "unique_key",
        type: DataTypes.STRING,
      },
      data: {
        type: DataTypes.JSONB,
        allowNull: false,
      },
      status: { type: DataTypes.ENUM("error", "success") },
      updatedAt: {
        type: DataTypes.DATE,
        field: "updated_at",
        allowNull: false,
      },
      createdAt: {
        type: DataTypes.DATE,
        field: "created_at",
        allowNull: false,
      },
    },
    {
      sequelize,
      paranoid: false,
      underscored: false,
      modelName: "GatewayLog",
      indexes: [
        {
          fields: ["unique_key"],
        },
      ],
    }
  );
  return GatewayLog;
};
```