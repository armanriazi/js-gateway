const amqplib = require('amqplib/callback_api')

amqplib.connect('amqp://localhost', function (error0, connection) {
  const queue = 'summarization'
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
      let result = {
        Result: ' به گزارش ایسنا و به نقل از مدیکال‌اکسپرس، اگر بدانید تا زمان آغاز ابتلا به آلزایمر چقدر فرصت دارید، چه کار می‌کنید ؟  روش پیشنهادی آنها، خواب عمیق و خوابیدن به اندازه کافی است .  وی افزود: نکته مهم پژوهش ما این است که نشان می‌دهد می‌توانیم کاری برای درست کردن این شرایط انجام دهیم .  نتایج پژوهش نشان داد آمیلوئید بتا در مغز شرکت‌کنندگانی که خواب نامنظمی طی دوره بررسی داشتند، افزایش یافته است .  وینر گفت: ما می‌توانیم به جای سال‌ها صبر کردن برای ابتلای افراد به آلزایمر، با ارزیابی کیفیت خواب آنها، تغییر در پلاک‌های آمیلوئید بتا را پیش‌بینی کنیم .',
      }

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
