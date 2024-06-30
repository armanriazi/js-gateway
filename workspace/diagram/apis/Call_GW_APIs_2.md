گیت‌وی یک سرویس نوشته شده با استفاده از فریم‌ورک Express بر روی محیط Node.js  است که به منظور مدیریت و پیاده‌سازی  API Gateway به کار می‌رود. این سرویس از پایگاه داده PostgreSQL برای ذخیره‌سازی اطلاعات اصلی خود استفاده می‌کند و پنج موجودیت اصلی را به نمایندگی از مفاهیم مختلف در در برنامه ی ما دارد. این موجودیت‌ها عبارتند از:

1. Users (کاربران): این موجودیت نمایانگر کاربران سیستم است. هر کاربر می‌تواند چندین کلید API (API Keys) داشته باشد.

2. API Keys (کلیدهای API): این موجودیت نمایانگر کلیدهای API است که برای کاربران تولید می‌شود. هر کلید API به یک کاربر (user_id) تعلق دارد و می‌تواند به یک یا چندین سرویس (service_id) اختصاص داده شود.

3. Projects (پروژه‌ها): این موجودیت نمایانگر پروژه‌های موجود در سیستم است و می‌تواند شامل چندین سرویس باشد.

4. Services (سرویس‌ها): این موجودیت نمایانگر سرویس‌های موجود در پروژه‌ها است و به یک پروژه (project_id) تعلق دارد. همچنین می‌تواند به یک کلید API (apikeyId) در جدول APIs اختصاص داده شود.

5. APIs: این موجودیت نمایانگر API‌هاست که به یک سرویس (service_id) و یک کلید API متعلق می‌شود. هر apiKey به یک service تعلق خواهد داشت.


این سرویس همچنین دارای یه موجودیت برای لاگ است که نتایج درخواست و پاسخ آن ها را ذخیره میکند.

- Gateway Logs (لاگ‌های گیت‌وی): این موجودیت نمایانگر لاگ‌های پاسخ‌های API است.


![entities](../../assets/uploads/371ed5f54566e751ca593601d17ad3fe/entities.png)


برای استفاده از هر api که منجر به ایجاد، حذف، دریافت و یا به روز رسانی موجودیت ها میشود باید پیش از آن با استفاده از api login یک توکن دریافت کرد که با استفاده از **JWT** ایجاد شده و یک session expiration یک ساعته دارد. 

[در مستندات اصلی گیت وی](https://armanriazi.github.io/js-gateway/apimarket/gateway/-/wikis/%D8%AF%D8%A7%DA%A9%DB%8C%D9%88%D9%85%D9%86%D8%AA-%DA%AF%DB%8C%D8%AA-%D9%88%DB%8C) درخصوص تمامی api های این سرویس به تفصیل توضیح داده شده است. 


برای نمونه: 


**Route**

*http method*: POST

`{{prod}}/users/login`

**Parameters**

`{ "username": "test", "password": "test123@A" }`

نوع داده ورودی برای اعتبار سنجی ایجاد کاربر، یک شیء با ویژگی های زیر است:

**Attributes:**

* username: Required. یک رشته با حداقل طول ۵ کاراکتر و حداکثر طول ۳۶ کاراکتر. این ویژگی اجباری است.
* password: Required. یک رشته که باید با استفاده از عبارت منظم خاصی الگویی را دنبال کند. رمز عبور باید حداقل شامل یک حرف کوچک، یک حرف بزرگ، یک رقم و یک کاراکتر ویژه باشد. رشته باید حداقل طول ۸ کاراکتر داشته باشد. این ویژگی اجباری است.


![Clipboard_-_April_5__2023_4_35_PM](../../assets/uploads/f07fa3b910078f0cd276899865c213f8/Clipboard_-_April_5__2023_4_35_PM.png)


و پاسخ این api یک توکن است:

```
Response:
  {
        "status": "success",
        "message": {
            "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6ImI1Y2EyNzYzLTIyYzctNDkzOC05ZGYwLTIyMTFkOTliN2U4NiIsImlhdCI6MTY5OTY4MzA4OH0.i1v0YZfKWaDZ3_uvNeelUoXPlr_qUy9UINaJTWfURP0"
        }
    }
```

***پیش از هر درخواست میدل وری ابتدا وجود توکن در هدر درخواست را چک کند سپس payload آن توکن را اعتبارسنجی کرده که یک توکن معتبر باشد.***


برای تعریف و تنظیم یک سرویس پشت گیت وی باید مراحل زیر را انجام داد:

1- ابتدا باید user ای تعریف گردد.

**Route**

*http method*: POST

`{{prod}}/api/users`

```
Headers:
  Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
```

**Parameters**

`{ "username": "test", "password": "test123@A", "role": "user" }`


2- سپس برای آن user یک apiKey ایجاد کرد.

**Route**

*http method*: POST

`{{prod}}/api/apikeys`

```
Headers:
  Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
```

**Parameters**

```
{ 
     "UserId": "2b721c8f-523e-4cc9-91d1-7a8eefd0c810", 
     "maxCount": 1000, 
     "expireDate": "24h" 
}
```

3- در این مرحله یک پروژه تعریف میشود که سرویس مد نظر را به آن تخصیص داد.


**Route**

*http method*: POST

`{{prod}}/api/projects`

```
Headers:
  Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
```

**Parameters**

```{ "name": "sgw" }```

4- سپس سرویس را تعریف میکنیم.

**Route**

*http method*: POST

`{{prod}}/api/services`

```
Headers:
  Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
```

**Parameters**

```
{
    "name": "pr-speech-asr-sgw-largefile-srt",
    "ProjectId": "b565741c-ec21-4463-90e0-0f7a77efe59f",
    "validation": {},
    "apis": [
        {
            "path": "pr-speech-asr-sgw-largefile-srt/largeFile",
            "files": [
                {
                    "name": "file",
                    "type": "voice",
                    "howGet": "file",
                    "required": true
                }
            ],
            "method": [
                "post"
            ]
        },
        {
            "path": "pr-speech-asr-sgw-largefile-srt/largeFile/*",
            "method": [
                "get"
            ]
        }
    ],
    "type": "direct",
    "host": "192.168.65.1:82/backend-selfhost/service/v4-speechRecognition",
    "protocol": "https",
    "rejectUnauthorized": false
}

```

5- در نهایت apiKey را به service تعریف شده تخصیص میدهیم.


**Route**

*http method*: POST

`{{prod}}/api/apis`

```
Headers:
  Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
```

**Parameters**

```
[
    {
        "ApiKeyId": "24b10a67-cf89-4d49-8225-8c9727caf818",
        "ServiceId": "3dca6624-624c-48bc-975c-ea9bdb16dac8",
        "apis": [
            {
                "path": "speech-synthesys@3/speech-synthesys",
                "method": [
                    "post"
                ]
            },
            {
                "path": "speech-synthesys@3/*",
                "method": [
                    "get"
                ]
            },
            {
                "path": "speech-synthesys@3/download/*",
                "method": [
                    "get"
                ]
            },
            {
                "path": "speech-synthesys@3/longText",
                "method": [
                    "post"
                ]
            },
            {
                "path": "speech-synthesys@3/trackingFile/*",
                "method": [
                    "get"
                ]
            }
        ],
        "maxCount": 10000,
        "TPM": 100
    }
]
```


**در تعریف هر apiKey باید expireDate و maxCount آن را مشخص کنیم**


```
{
    "UserId": "a8a5e0a1-3b82-45be-9fa2-54885bf64920",
    "maxCount": 20000,
    "expireDate": "90d"
}
```



**در تعریف هر سرویس باید نوع آن را مشخص کنیم**

***نکات:*** 

− در شرایط فعلی ۲ نوع درخواست برای هر سرویس مشخص کردیم که میتواند **direct** و یا **proxy** باشد.

− **نام سرویس باید حتما با path تعریف شده در apis یکسان باشند** چون این دو با هم مقایسه میشوند.



![image](../../assets/uploads/19e98644989d1cbd1d47726d64e44c7e/image.jpg)


- اگر سرویس مورد نظر مبتنی بر ارسال فایل بود باید در این بخش(files) ذکر شود که فایل آن چه نامی خواهد داشت، چه نوعی است(video, image, voice) اجباری است یا خیر، ...

![image_file](../../assets/uploads/36f49f7251655b36ee68d719c1fd5022/image_file.jpg)


در غیر اینصورت اگر سرویس nlp بود، بدون این فیلد باید سرویس را تعریف کرد.

- نوع سرویس و درخواست های آن نیز باید در تعریف سرویس ذکر گردد و این تعیین کننده نحوه ی پاسخ گیت وی به درخواست های ورودی است.

اگر تایپ سرویس direct باشد یعنی درخواست از طریق rabbitMQ و صف با AI در ارتباط خواهد بود و باید اسم صف آن ذکر شود. در این حالت فایل از طریق **busboy** آپلود میشود.

![image](../../assets/uploads/8d97f471c5c6f6b291842bf4abf6d09c/image.png)

و اگر proxy باشد یعنی درخواست های این سرویس به api ریموت دیگری پراکسی میشود و باید host, protocol و rejectUnauthorized(که بیانگر secure بودن/نبودن آن است) در تعریف سرویس بیان شود.

![image](../../assets/uploads/3558b41883a40f6b6383c90b7feb93d6/image.png)


- در تعریف سرویس میتوان برای آن validation ورودی آن سرویس را نیز تعریف کرد که در بکند با **ajv validator **پیاده سازی شده است.


```
 "validation": {
                    "type": "object",
                    "required": [
                        "srt"
                    ],
                    "properties": {
                        "srt": {
                            "enum": [
                                "1",
                                "true",
                                "True",
                                true,
                                false,
                                1,
                                0,
                                "false",
                                "False",
                                "0"
                            ]
                        }
                    },
                    "errorMessage": {
                        "type": "نوع ورودی نادرست است",
                        "required": {
                            "srt": "srt الزامی است"
                        },
                        "properties": {
                            "srt": "ورودی یکی از مقادیر روبرو میباشد ['1', 'true', 'True', true, false, 1, 0, 'false', 'False', '0']"
                        }
                    },
                    "additionalProperties": true
                }
```

گیت‌وی عملکرد خود را بر اساس نام سرویس (serviceName) که دومین المان از URL درخواستی میباشد، تعیین می‌کند. بر اساس این نام سرویس، گیت‌وی اطلاعات مربوط به سرویس مورد نظر را استخراج می‌کند تا بداند با درخواست به چه نحوی باید رفتار کند.


![image__1_](../../assets/uploads/f6fa5f8ca673c637724fdd02f3044a84/image__1_.jpg)





***همه درخواست ها به سرویس ها باید حاوی apikey باشد مگر ip های استثنا***


***در تعریف apis میتوان maxCount,  (transaction per minute)TPM و policies را تعریف کرد.***


- شمای تعریف policies به صورت زیر است: 

```
{
  "image": {
    "size": 1200000
  },
  "video": {
    "size": 100000,
    "duration": 14
  },
  "voice": {
    "size": 16,
    "duration": 10
  },
  "character": {
    "counter": 6000
  }
}
```

***گیت وی همچنین قابلیت استفاده از Grpc و Websocket را نیز دارد.***





فرایند کامل استفاده از کنترلر های ارتباط با هوش [در این کامیونت](https://armanriazi.github.io/js-gateway/apimarket/gateway/-/wikis/gateway-UML-document) ذکر شده است.