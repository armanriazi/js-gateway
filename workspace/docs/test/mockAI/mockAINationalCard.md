const amqplib = require('amqplib/callback_api')

amqplib.connect('amqp://localhost', function (error0, connection) {
  const queue = 'nationalCart'
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
        imagePath: `${path.file}`,
        checksum: 'f831869a0e0f919dbc5a6a0f501f2fc0c317215c',
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
