const amqplib = require('amqplib/callback_api')
const fs = require('fs');
const path = require('path');


amqplib.connect('amqp://localhost', function (error0, connection) {
  const queue = 'faceCartoonize'
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
      const filePath = JSON.parse(msg.content)

      // Define the source and destination paths
      const sourcePath = filePath.file;

      const destinationPath = path.join(`${process.cwd()}`, '../archive/files/', sourcePath.split('/').at(-1))

      const resultPath =  path.join('files/', sourcePath.split('/').at(-1))

      // Copy the file
      fs.copyFile(sourcePath, destinationPath, (err) => {
        if (err) {
          console.error('Error occurred while copying file:', err);
        } else {
          console.log('File copied successfully!');
        }
      });

      let result = {
        imagePath: resultPath,
        checksum: 'f831869a0e0f919dbc5a6a0f501f2fc0c317215c',
      }

      console.log(msg.content.toString(), sourcePath,'    **MESSAGE***')

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
