const amqplib = require('amqplib/callback_api')
const fs = require('fs');


amqplib.connect('amqp://localhost', function (error0, connection) {
  const queue = 'card'
  if (error0) {
    throw error0
  }
  connection.createChannel(function (error1, channel) {
    if (error1) {
      throw error1
    }
    console.log(' [x] Awaiting RPC requests')

    channel.assertQueue(queue, {
      durable: true,
    })
    channel.prefetch(1)

    channel.consume(queue, function reply(msg) {
      var message = JSON.parse(msg.content.toString())

      // const file = fs.statSync(message.file)

      const result = {
        // size: file.size,
        status: 'success',
        message: {
          imagePath: '20230228_055813_687861_gikumfhayt/out.jpg',
          cardNumber: {
            value: '6219861061666615',
            detectionConfidence: 0.87,
            recognitionConfidence: 99,
            validity: true,
            rectangle: {
              left: 103,
              top: 559,
              width: 1325,
              height: 135,
            },
            imagePath: '20230228_055813_687861_gikumfhayt/16.jpg',
          },
          logo: {
            bankName: 'سامان',
            detectionConfidence: 0.83,
            recognitionConfidence: 1,
            rectangle: {
              left: 1748,
              top: 63,
              width: 194,
              height: 189,
            },
            imagePath: '20230228_055813_687861_gikumfhayt/logo.jpg',
          },
          expirationDate: {
            value: '1406/05',
            detectionConfidence: 0.87,
            recognitionConfidence: 99,
            rectangle: {
              left: 105,
              top: 743,
              width: 289,
              height: 92,
            },
            imagePath: '20230228_055813_687861_gikumfhayt/date.jpg',
          },
          cardCVV2: {
            value: '945',
            detectionConfidence: 0.82,
            recognitionConfidence: 97,
            rectangle: {
              left: 1380,
              top: 736,
              width: 154,
              height: 92,
            },
            imagePath: '20230228_055813_687861_gikumfhayt/cvv2.jpg',
          },
          cardOwner: {
            value: 'کالطاطها خان غلام کایا ',
            detectionConfidence: 0.85,
            recognitionConfidence: 63,
            rectangle: {
              left: 94,
              top: 1067,
              width: 806,
              height: 108,
            },
            imagePath: '20230228_055813_687861_gikumfhayt/name.jpg',
          },
        },
      }

      channel.sendToQueue(
        msg.properties.replyTo,
        Buffer.from(JSON.stringify({ result })),
        {
          correlationId: msg.properties.correlationId,
        }
      )

      channel.ack(msg)
      // }
    })
  })
})
