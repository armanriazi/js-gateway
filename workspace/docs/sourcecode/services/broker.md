
```javascript



import { randomUUID } from "crypto";
import EventEmitter from "events";
import chalk from "chalk";
import amqp from "amqplib";
import env from "../common/env.js";
let instance;

/**
 * representive class of Authentication
 * @class
 */
export default class Rabbit {
  /**
   * @constructor
   * @param {Object} connection - RabbitMQ connection object.
   * @param {Object} channel - RabbitMQ channel object.
   * @param {string} replyQueue - Name of RabbitMQ queue for reply messages.
   */
  constructor(connection, channel, replyQueue) {
    this.connection = connection;
    this.channel = channel;
    this.replyQueue = replyQueue;
  }

  /**
   * @function init
   * @description Initializes a RabbitMQ connection and returns a new instance of the Rabbit class.
   * @return {Promise<Rabbit>} A promise that resolves with a new instance of the Rabbit class.
   */
  async init() {
    try {
      const connection = await amqp.connect({
        protocol: env("AI_BROKER_PROTOCOL"),
        hostname: env("AI_BROKER_ADDRESS"),
        port: Number(env("AI_BROKER_PORT")),
        heartbeat: Number(env("AI_BROKER_HEARTBEAT")),
      });

      console.log(
        chalk.bold.cyan(`
        ----------------------------
            AI BROKER CONNECTED
        ----------------------------`)
      );

      connection.on("error", (err) => {
        throw new Error(`ERROR OCURRED ON BROKER CONNECTION : ${err.stack}`);
      });

      const channel = await connection.createChannel();

      channel.on("close", () => {
        throw new Error("CHANNEL CLOSED");
      });

      channel.responseEmitter = new EventEmitter();

      channel.responseEmitter.setMaxListeners(0);

      const replyQueue = await this.createRandomQueue(channel);

      await this.getMessageFromQueue(channel, replyQueue);

      return new Rabbit(connection, channel, replyQueue);
    } catch (e) {
      // eslint-disable-next-line no-console
      console.error(`ERROR IN CONNECTING TO AI BROKER : ${e.stack}`);
    }
  }

  /**
   * @static
   * @function getInstance
   * @description Returns an instance of the Rabbit class. If no instance exists, a new one is created.
   * @return {Promise<Rabbit>} A promise that resolves with an instance of the Rabbit class.
   */
  static async getInstance() {
    if (!instance) {
      const broker = new Rabbit();

      instance = await broker.init();
    }

    return instance;
  }

  /**
   * @function createRandomQueue
   * @description Creates a random queue on the RabbitMQ channel and returns the queue name.
   * @param {Object} channel - RabbitMQ channel object.
   * @return {Promise<string>} A promise that resolves with the name of the created queue.
   */
  async createRandomQueue(channel) {
    const replyQueue = await channel.assertQueue("", { exclusive: true });
    return replyQueue.queue;
  }

  /**
   * @function sendRpcMessage
   * @description Sends an RPC message to the RabbitMQ queue and waits for a response on the reply queue.
   * @param {Object} msg - Message object to be sent to the queue.
   * @return {Promise} A promise that resolves with the response from the RPC call.
   */
  sendRpcMessage({ msg, queue }) {
    return new Promise((resolve, reject) => {
      try {
        const correlationId = randomUUID();
        this.channel.responseEmitter.once(correlationId, resolve);
        this.channel.sendToQueue(queue, Buffer.from(JSON.stringify(msg)), {
          correlationId,
          replyTo: this.replyQueue,
        });
      } catch (e) {
        reject(e);
      }
    });
  }

  /**
   * به دست آوردن تعداد پردازش های در صف
   * @function messageCount
   * @description به دست آوردن تعداد پردازش های در صف
   * @param {string} serverQueueName - اسم صف
   * @return {calback} responseHandler
   * @memberof rabbitmq
   */
  async messageCount(serverQueueName) {
    if (!this.channel) {
      return { messageCount: null };
    }
    const queue = await this.channel.checkQueue(serverQueueName);
    return queue;
  }

  /**
   * @function sendMessage
   * @description Sends a message to a specified RabbitMQ queue.
   * @param {Object} options - Object containing the name of the queue and the message to be sent.
   * @param {string} options.queue - Name of the RabbitMQ queue to send the message to.
   * @param {Object} options.msg - Message object to be sent to the queue.
   * @return {Promise<boolean>} A promise that resolves with a boolean indicating whether the message was sent successfully.
   */
  sendMessage({ queue, msg }) {
    return this.channel.sendToQueue(queue, Buffer.from(JSON.stringify(msg)));
  }

  /**
   * @function getMessageFromQueue
   * @description Listens for messages on the RabbitMQ reply queue and emits them to the response emitter.
   * @param {string} channel - created rabbitmq channel
   * @param {string} replyQueue - incoming messages queue
   */
  getMessageFromQueue(channel, replyQueue) {
    return channel.consume(
      replyQueue,
      (msg) => {
        channel.responseEmitter.emit(
          msg.properties.correlationId,
          msg.content.toString()
        );
      },
      { noAck: true }
    );
  }
}
```