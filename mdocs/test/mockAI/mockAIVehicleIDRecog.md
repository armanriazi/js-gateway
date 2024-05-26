const amqplib = require('amqplib/callback_api')

amqplib.connect('amqp://localhost', function (error0, connection) {
  const queue = 'vehicleIDCardRecognition'
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
      var path = JSON.parse(msg.content.toString())

      let result = {
        inputName: 'gateway-file-4cf62f11-24aa-4235-90b2-465271405731.jpg',
        imagePath:
          'gateway-file-4cf62f11-24aa-4235-90b2-465271405731/gateway-file-4cf62f11-24aa-4235-90b2-465271405731.jpg',
        Data: {
          Grade: {
            value: 'پایه سوم',
            rectangle: {
              left: 757,
              top: 218,
              width: 204,
              height: 77,
            },
          },
          NationalCode: {
            value: '0015431282',
            rectangle: {
              left: 562,
              top: 325,
              width: 321,
              height: 74,
            },
          },
          Name: {
            value: 'علی آقاپور',
            rectangle: {
              left: 886,
              top: 509,
              width: 239,
              height: 89,
            },
          },
          BirthDate: {
            value: '1371/10/16',
            rectangle: {
              left: 670,
              top: 609,
              width: 244,
              height: 57,
            },
          },
          ExportationDate: {
            value: '1396/10/04',
            rectangle: {
              left: 654,
              top: 702,
              width: 258,
              height: 59,
            },
          },
          LicenseNumb: {
            value: '9605508601',
            rectangle: {
              left: 523,
              top: 785,
              width: 324,
              height: 56,
            },
          },
          Serial: {
            value: '71324026396',
            rectangle: {
              left: 131,
              top: 634,
              width: 419,
              height: 62,
            },
          },
          Validation: {
            value: '10سال',
            rectangle: {
              left: 125,
              top: 703,
              width: 156,
              height: 67,
            },
          },
        },
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
