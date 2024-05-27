
|   Name    |  Type  |       Description        |
| :-------: | :----: | :----------------------: |
|    id     | column |            -             |
| uniqueKey | column |            -             |
|  status   | column | ENUM('error', 'success') |
|   data    | column |          JSONB           |


```javascript



/** @type {import('sequelize-cli').Migration} */
module.exports = {
  async up(queryInterface, Sequelize) {
    await queryInterface.createTable(
      'GatewayLogs',
      {
        id: {
          type: Sequelize.UUID,
          allowNull: false,
          primaryKey: true,
        },
        uniqueKey: {
          allowNull: true,
          field: 'unique_key',
          type: Sequelize.STRING,
        },
        status: { type: Sequelize.ENUM('error', 'success') },
        data: { type: Sequelize.JSONB, allowNull: false },
        updatedAt: {
          type: Sequelize.DATE,
          field: 'updated_at',
          allowNull: false,
        },
        createdAt: {
          type: Sequelize.DATE,
          field: 'created_at',
          allowNull: false,
        },
      }
    )
    await queryInterface.addIndex('GatewayLogs', ['unique_key'])
  },

  async down(queryInterface) {
    await queryInterface.dropTable('GatewayLogs')
  },
}
