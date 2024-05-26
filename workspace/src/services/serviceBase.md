
```javascript



/**
 * representive class of basic services
 * @class
 */
export default class serviceBase {
  constructor(model) {
    this.model = model;
  }

  /**
   * create an instance of an entity in DB
   * @type {function}
   * @param {object} data
   * @param {object} transaction
   */
  create(data, transaction = {}) {
    return this.model.create(data, transaction);
  }

  /**
   * create many instances of an entity in DB
   * @type {function}
   * @param {object} data
   */
  bulkCreate(data) {
    return this.model.bulkCreate(data);
  }

  /**
   * find instances of an entity in DB
   * @type {function}
   * @param {object}
   */
  find({ where, pagination, attributes, group, order, paranoid }) {
    return this.model.findAll({
      where,
      attributes,
      group,
      order,
      paranoid,
      ...pagination,
    });
  }

  /**
   * find and counts instances of an entity in DB
   * @type {function}
   * @param {object}
   */
  findAndCount({ where, pagination, attributes, group, order, paranoid }) {
    return this.model.findAndCountAll({
      where,
      attributes,
      group,
      order,
      paranoid,
      ...pagination,
    });
  }

  /**
   * find one instance of an entity in DB
   * @type {function}
   * @param {object} where - search condition
   * @param {object} attributes - list of requested columns
   */
  findOne(where, attributes) {
    return this.model.findOne({ where, attributes });
  }

  /**
   * update one instance of an entity in DB
   * @type {function}
   * @param {object} data - data that must be updated
   * @param {object} where - search condition
   * @param {boolean} returning - return on not return row after update
   * @param {object} transaction - execute or commit a group of operations as a unit
   */
  updateOne(data, where, returning, transaction) {
    return this.model.update(data, { where, transaction, returning });
  }

  /**
   * delete one instance of an entity in DB
   * @type {function}
   * @param {object} where - search condition
   * @param {boolean} returning - return on not return row after update
   * @param {object} transaction - execute or commit a group of operations as a unit
   */
  deleteOne({ where, returning, transaction = {}, force }) {
    return this.model.destroy({ where, returning, force }, transaction);
  }

  /**
   * restoreOne one instance of an entity in DB
   * @type {function}
   * @param {object} where - search condition
   */
  restoreOne(where) {
    return this.model.restore({ where });
  }

  /**
   * find or create one instance of an entity in DB
   * @type {function}
   * @param {object} where - search condition
   * @param {object} defaults - in case of no data, an instance will be created with defaults valuses
   */
  findOrCreate({ where, defaults }) {
    return this.model.findOrCreate({ where, defaults });
  }
}
```