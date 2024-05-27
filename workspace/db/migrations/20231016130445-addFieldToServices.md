
|        Name        |                      Type                      |   Description    |
| :----------------: | :--------------------------------------------: | :--------------: |
|        type        |       column, value: 'proxy' or 'direct'       | with constraints |
|        host        |                     column                     |        -         |
|      protocol      |        column, value: 'http' or 'https'        |        -         |
| rejectUnauthorized | column, value: {'false' : http, 'true': https} | with constraint  |



```javascript



/** @type {import('sequelize-cli').Migration} */
module.exports = {
  up(queryInterface, Sequelize) {
    return Promise.all([
      queryInterface.addColumn('Services', 'type', {
        type: Sequelize.STRING,
        allowNull: true,
      }),
      queryInterface.addColumn('Services', 'host', {
        type: Sequelize.STRING,
        allowNull: true,
      }),
      queryInterface.addColumn('Services', 'protocol', {
        type: Sequelize.STRING,
        allowNull: true,
      }),
      queryInterface.addColumn('Services', 'rejectUnauthorized', {
        type: Sequelize.BOOLEAN,
        allowNull: false,
        defaultValue: false
      }),
      queryInterface.addConstraint('Services', {
        fields: ['type'],
        type: 'check',
        where: {
          [Sequelize.Op.or]: [
            {
              type: 'proxy',
              host: { [Sequelize.Op.not]: null },
              protocol: { [Sequelize.Op.not]: null },
            },
            {
              type: 'direct',
              queue: { [Sequelize.Op.not]: null },
            },
          ],
        },
        name: 'type_change_constraints',
        message: 'Invalid field values for changing service type.',
      }),
      queryInterface.addConstraint('Services', {
        fields: ['protocol', 'rejectUnauthorized'],
        type: 'check',
        where: {
          [Sequelize.Op.or]: [
            {
              protocol: 'http',
              rejectUnauthorized: false,
            },
            {
              protocol: 'https',
              rejectUnauthorized: { [Sequelize.Op.in]: [true, false] },
            },
          ],
        },
        name: 'prevent_update_rejectUnauthorized_for_http_protocol',
        message: 'RejectUnauthorized must be false for HTTP protocols, and can be true or false for HTTPS protocols.',
      }),
    ])
  },

  down(queryInterface) {
    return Promise.all([
      queryInterface.removeConstraint('Services', 'type_change_constraints'),
      queryInterface.removeConstraint('Services', 'prevent_update_rejectUnauthorized_for_http_protocol'),
      queryInterface.removeColumn('Services', 'type'),
      queryInterface.removeColumn('Services', 'host'),
      queryInterface.removeColumn('Services', 'protocol'),
      queryInterface.removeColumn('Services', 'rejectUnauthorized')
    ])
  },
}
```
