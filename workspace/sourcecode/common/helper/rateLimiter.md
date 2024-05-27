
```javascript



// import rateLimit from 'express-rate-limit'
// import messages from '../messages'
// import env from '../env'

// /**
//  * @function rateLimiter
//  * @description verifies if received token is valid or not
//  * @param {string} RATE_LIMITER_PERIOD - specific time period for limiting
//  * @param {string} RATE_LIMITER_MAX_REQUESTS - maximum requests in a specific time period
//  * @return {object}
//  */

// const limiter = rateLimit({
//   windowMs: Number(env('RATE_LIMITER_PERIOD')),
//   max: Number(env('RATE_LIMITER_MAX_REQUESTS')),
//   standardHeaders: true,
//   legacyHeaders: false,
//   skip: (request, response) => false,
//   handler: function(req, res /*, next*/) {
//     throw global.error(400, messages.m.TOO_MANY_REQUESTS)
//   },
// })

// export default limiter
```