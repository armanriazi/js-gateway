const amqplib = require('amqplib/callback_api')

amqplib.connect('amqp://localhost', function (error0, connection) {
  const queue = 'face5'
  if (error0) {
    throw error0
  }
  connection.createChannel(function (error1, channel) {
    if (error1) {
      throw error1
    }

    console.log(' [x] Awaiting RPC requests')

    channel.assertQueue(queue, {
      durable: false,
    })
    channel.prefetch(1)

    channel.consume(queue, function reply(msg) {
      var path = JSON.parse(msg.content.toString())

      let result = {
        faces: [
          {
            detectionConfidence: 0.885,
            imagePath:
              '20230228_061034_443536_jvwhorbybg/face0.jpg',
            faceAttributes: {
              emotion: {
                anger: 0.001,
                disgust: 0,
                fear: 0.001,
                happiness: 0.987,
                sadness: 0.001,
                surprise: 0.001,
                neutral: 0.01,
              },
              gender: 'Male',
              age: 36,
              headPose: {
                roll: '8.6',
                yaw: '-2.8',
                pitch: '-2.7',
              },
            },
          },
        ],
        imagePath:
          '20230228_061034_443536_jvwhorbybg/overallImage.jpg',
      }

      console.log(msg.content.toString(), '    **MESSAGE***')

      channel.sendToQueue(
        msg.properties.replyTo,
        Buffer.from(JSON.stringify({ result })),
        {
          correlationId: msg.properties.correlationId,
        }
      )

      channel.ack(msg)
    })
  })
})
