## Middleware

تنظیمات اولیه مربوط به قرار دادن middleware ها می باشد که میبایست ترتیب آن رعایت گردد.

```javascript

//...

app.use(createLog)

app.use(saveResultLog)

app.use(publicMiddlewares)

app.use(routers)
//...

‍‍‍```

## Winston

 یکی از کاربردهای این کتابخانه در برنامه گیتوی جهت نوشتن لاگ در در فایل error.log می باشد که در مسیر فولدر logs قرار گرفته است.

 برای مثال محتوا این فایل می تواند شامل ممیزی سازی خطاهای ذیل باشد:

```md
 **1403/2/25, 11:55:28 [error]:**  listen EADDRINUSE: address already in use :::3002 "Error: listen EADDRINUSE: address already in use :::3002\n    at Server.setupListenHandle [as _listen2] (node:net:1817:16)\n    at listenInCluster (node:net:1865:12)\n    at Server.listen (node:net:1953:7)\n    at Object.<anonymous> (/home/armanriazi/partdp-projects/gateway/gateway/src/index.js:9:8)\n    at Generator.next (<anonymous>)\n    at bl (/home/armanriazi/partdp-projects/gateway/node_modules/esm/esm.js:1)\n    at kl (/home/armanriazi/partdp-projects/gateway/node_modules/esm/esm.js:1)\n    at Object.u (/home/armanriazi/partdp-projects/gateway/node_modules/esm/esm.js:1)\n    at Object.o (/home/armanriazi/partdp-projects/gateway/node_modules/esm/esm.js:1)\n    at Object.<anonymous> (/home/armanriazi/partdp-projects/gateway/node_modules/esm/esm.js:1)"
**1403/2/25, 13:15:18 [error]:**  ERROR OCURRED ON BROKER CONNECTION : Error: Unexpected close
    at succeed (/home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:280:13)
    at onOpenOk (/home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:262:5)
    at /home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:165:32
    at /home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:159:12
    at Socket.recv (/home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:507:12)
    at Object.onceWrapper (node:events:631:28)
    at Socket.emit (node:events:517:28)
    at Socket.emit (node:domain:489:12)
    at emitReadable_ (node:internal/streams/readable:633:12)
    at process.processTicksAndRejections (node:internal/process/task_queues:81:21) "Error: ERROR OCURRED ON BROKER CONNECTION : Error: Unexpected close\n    at succeed (/home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:280:13)\n    at onOpenOk (/home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:262:5)\n    at /home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:165:32\n    at /home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:159:12\n    at Socket.recv (/home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:507:12)\n    at Object.onceWrapper (node:events:631:28)\n    at Socket.emit (node:events:517:28)\n    at Socket.emit (node:domain:489:12)\n    at emitReadable_ (node:internal/streams/readable:633:12)\n    at process.processTicksAndRejections (node:internal/process/task_queues:81:21)\n    at ChannelModel.<anonymous> (/home/armanriazi/partdp-projects/gateway/gateway/src/services/broker.js:47:15)\n    at ChannelModel.emit (node:events:517:28)\n    at ChannelModel.emit (node:domain:489:12)\n    at Connection.emit (node:events:517:28)\n    at Connection.emit (node:domain:489:12)\n    at C.onSocketError (/home/armanriazi/partdp-projects/gateway/node_modules/amqplib/lib/connection.js:361:10)\n    at Socket.emit (node:events:529:35)\n    at Socket.emit (node:domain:489:12)\n    at endReadableNT (node:internal/streams/readable:1400:12)\n    at process.processTicksAndRejections (node:internal/process/task_queues:82:21)"
```‍‍‍



## ذخیره سازی لاگ ها سمت دیتابیس

با توجه به api یا service بودن درخواست نوع ذخیره سازی مشخص می گردد. همیشه در نظر داشته باشید که با دیباگ کردن امکان دارد ترتیب اجرایی برای شما مشخص نگردد بدلیل async بودن توابع. اما در خصوص ذخیره درخواست و پاسخ ها به جدول GatewayLogs در نظر داشته باشید که هر نوع درخواستی که از سمت کلاینت یا api انجام شود حتما در جدول ذخیره می گردد و سپس جواب درخواست هم متعاقبا در همان رکورد اطلاعاتی آپدیت خواهد شد.


## استفاده از کتابخانه partbackendlogger

این کتابخانه به به endpoint ای متصل است که تنظیمات آن در فایل .env  انجام می شود. لذا با ایجاد هر لاگ می توانید به سمت سرور مشخص شده ارسال کنید و سپس اطلاعات ان در grafana مشاهده فرمایید. برای انجام تنظیمات دیگر میتوان از فایل backendLogger  در بخش سرویس ها استفاده نمایید.



## فایل  logger در بخش Service

 این فایل از کتابخانه partbackendlogger استفاده می نماید. یا میتوان گفت تکمیل کننده این سرویس در برنامه ما می باشد.لذا این فایل بخش نهایی لاگ هست و کتابخانه وینستون هم در کل پروژه تنها اینجا مصرف خواهد داشت.

 ## نحوه استفاده از سرویس لاگر


 ```javascript
 import logger from './backendLogger'

export default async ({ req, res, responseBody, errStack }) => {
  try {
    if (errStack) {
      // send to logger service
      logger.errorLog({ res, error: errStack })
 ```

## ساختار json بخش لاگ

```json
{
    "log" : {
       "requestId":{},
        "meta": {
          "userAgent": {},
          "ip": {},
          "method": {},
          "originalUrl": {},
          "path": {},
        },
        "data": {
          "status" : {},
          "message" : {},
          "request" : {},
          "response" : {},
        },
    }
}
```
