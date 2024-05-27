
```javascript



/**
 * @function paginationHandler
 * @description prepares pagination elements for indexing data from DB
 * @param {object} req - express request object
 * @param {object} res - express response object
 * @return {function} - returns a middleware that directs request to next middleware if request data is valida according to joi schema
 */
export default (req, res, next) => {
  try {
    const { page, limit } = req.query;

    req.query.offset = ((page || 1) - 1) * (limit || 10);

    req.query.limit = parseInt(limit || 10);

    delete req.query.page;

    next();
  } catch (err) {
    next(err);
  }
};
```