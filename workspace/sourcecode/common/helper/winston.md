
```javascript



import winston from "winston";

/**
 * representive class of general utility methods
 * @class
 */
export default new (class Logger {
  constructor() {
    const { combine, timestamp, printf, align, json } = winston.format;

    this.formatter = combine(
      timestamp({ format: this.getTimeZone() }),
      json(),
      align(),
      printf((info) => {
        const { timestamp, level, message, meta } = info;

        return `${timestamp} [${level}]: ${message} ${
          Object.keys(meta).length ? JSON.stringify(meta, null, 2) : ""
        }`;
      })
    );

    this.transport = new winston.transports.File({
      format: this.formatter,
      filename: "logs/error.log",
      level: "error",
    });

    this.logger = winston.createLogger({
      level: "error",
      transports: [this.transport],
    });
  }

  /**
   * @function getTimeZone
   * @description log registering time with time zone
   * @return {object} tehran's date and time for log record
   */
  getTimeZone() {
    const current = Date.now();
    const date = new Intl.DateTimeFormat("Fa-IR", {
      dateStyle: "short",
      numberingSystem: "latn",
      timeStyle: "medium",
      timeZone: "asia/tehran",
    });

    return date.format(current);
  }

  /**
   * @function error
   * @description error method
   * @param {string} msg - error message
   * @param {object} meta - err meta data to register in log record
   */
  error(msg, meta) {
    this.logger.error(msg, { meta });
  }
})();
```