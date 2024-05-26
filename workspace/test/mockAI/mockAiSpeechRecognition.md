const amqplib = require("amqplib/callback_api");

amqplib.connect("amqp://localhost", function (error0, connection) {
  const queue = "speechRecognition";
  if (error0) {
    throw error0;
  }
  connection.createChannel(function (error1, channel) {
    if (error1) {
      throw error1;
    }

    console.log(" [x] Awaiting RPC requests");

    channel.assertQueue(queue, {
      durable: false,
    });
    channel.prefetch(1);

    channel.consume(queue, function reply(msg) {
      var path = JSON.parse(msg.content.toString());

      let result = {
        data: {
          result:
            "در مورد سفارت این نکته رو بگم که ببینید اصلا نباید استرس داشته باشین، خود استرس شک برانگیزه به آپیسره یه چیزی ۱ ایمپالسی می ۱۰ که آق: این ۱ ریگی تو کفشش هست که استرس داره و یه مشکلی داره که قیافه اش مضطبه ",
          srt: "3d9c05c4-0e31-46af-bedd-8b6388c7dd73.srt",
          time_stamp: [
            {
              word: "در",
              start: 1.55,
              end: 1.59,
            },
            {
              word: "مورد",
              start: 1.91,
              end: 1.95,
            },
            {
              word: "سفارت",
              start: 2.15,
              end: 2.71,
            },
            {
              word: "این",
              start: 2.91,
              end: 2.95,
            },
            {
              word: "نکته",
              start: 3.03,
              end: 3.31,
            },
            {
              word: "رو",
              start: 3.35,
              end: 3.39,
            },
            {
              word: "بگم",
              start: 3.51,
              end: 3.83,
            },
            {
              word: "که",
              start: 3.95,
              end: 3.99,
            },
            {
              word: "ببینید",
              start: 4.95,
              end: 5.35,
            },
            {
              word: "اصلا",
              start: 6.23,
              end: 6.27,
            },
            {
              word: "نباید",
              start: 6.59,
              end: 6.75,
            },
            {
              word: "استرس",
              start: 6.91,
              end: 7.19,
            },
            {
              word: "داشته",
              start: 7.39,
              end: 7.43,
            },
            {
              word: "باشین",
              start: 7.75,
              end: 7.95,
            },
            {
              word: "خود",
              start: 9.59,
              end: 9.63,
            },
            {
              word: "استرس",
              start: 10.03,
              end: 10.51,
            },
            {
              word: "شک",
              start: 10.83,
              end: 10.87,
            },
            {
              word: "برانگیزه",
              start: 11.19,
              end: 11.91,
            },
            {
              word: "به",
              start: 12.95,
              end: 12.99,
            },
            {
              word: "آپیسره",
              start: 13.11,
              end: 13.63,
            },
            {
              word: "یه",
              start: 13.83,
              end: 13.95,
            },
            {
              word: "چیزی",
              start: 14.07,
              end: 14.31,
            },
            {
              word: "۱",
              start: 14.67,
              end: 14.71,
            },
            {
              word: "ایمپالسی",
              start: 14.79,
              end: 15.31,
            },
            {
              word: "می",
              start: 15.43,
              end: 15.47,
            },
            {
              word: "۱۰",
              start: 15.55,
              end: 15.59,
            },
            {
              word: "که",
              start: 15.71,
              end: 15.75,
            },
            {
              word: "آق",
              start: 16.07,
              end: 16.11,
            },
            {
              word: "این",
              start: 16.39,
              end: 16.43,
            },
            {
              word: "۱",
              start: 17.03,
              end: 17.07,
            },
            {
              word: "ریگی",
              start: 19.01,
              end: 19.25,
            },
            {
              word: "تو",
              start: 19.69,
              end: 19.73,
            },
            {
              word: "کفشش",
              start: 19.85,
              end: 20.25,
            },
            {
              word: "هست",
              start: 20.49,
              end: 20.53,
            },
            {
              word: "که",
              start: 20.65,
              end: 20.69,
            },
            {
              word: "استرس",
              start: 21.21,
              end: 21.57,
            },
            {
              word: "داره",
              start: 21.65,
              end: 21.81,
            },
            {
              word: "و",
              start: 21.89,
              end: 21.93,
            },
            {
              word: "یه",
              start: 22.01,
              end: 22.13,
            },
            {
              word: "مشکلی",
              start: 22.33,
              end: 22.61,
            },
            {
              word: "داره",
              start: 22.73,
              end: 22.89,
            },
            {
              word: "که",
              start: 23.05,
              end: 23.09,
            },
            {
              word: "قیافه",
              start: 24.61,
              end: 25.05,
            },
            {
              word: "اش",
              start: 25.17,
              end: 25.21,
            },
            {
              word: "مضطبه",
              start: 26.61,
              end: 27.25,
            },
          ],
          rtf: 0.16771077453896105,
        },
      };

      console.log(msg.content.toString(), "    **MESSAGE***");

      channel.sendToQueue(
        msg.properties.replyTo,
        Buffer.from(JSON.stringify({ result })),
        {
          correlationId: msg.properties.correlationId,
        },
      );

      channel.ack(msg);
    });
  });
});
