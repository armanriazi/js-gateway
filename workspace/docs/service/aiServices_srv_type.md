
زمانی می توانیم از درخواست های پروکسی استفاده نماییم که مقصد سرویس باشد و نه Api لذا چون قرار است پروکسی به سرویس ارسال شود لذا باید سرویسی انتخاب گردد که بخش queue خالی باشد. در نهایت سایر مشخصات پروکسی را باید تکمیل شده دید مثل ادرس هاست و نام سرویس. نمونه تکمیلی:

```‍‍‍json
{
    "data": {
        "status": "success",
        "message": [
            {
                "id": "bba01470-238c-4738-83f8-b5b43ead2b68",
                "ApiKeyId": "84d017d6-f239-40bf-ac7b-c76b86a281c9",
                "ServiceId": "cd9abaf5-be53-43c3-b447-a336e8eb184f",
                "apis": [
                    {
                        "path": "imageEditing/files",
                        "method": [
                            "post"
                        ]
                    },
                    {
                        "path": "imageEditing/trackingFile/*",
                        "method": [
                            "get"
                        ]
                    },
                    {
                        "path": "imageEditing/checkServerHealth",
                        "method": [
                            "get"
                        ]
                    },
                    {
                        "path": "imageEditing/*",
                        "method": [
                            "get"
                        ]
                    }
                ],
                "TPM": 500,
                "maxCount": -1,
                "policies": null,
                "createdAt": "2024-05-07T14:11:18.589Z",
                "updatedAt": "2024-05-07T14:11:18.589Z",
                "Service": {
                    "id": "cd9abaf5-be53-43c3-b447-a336e8eb184f",
                    "name": "imageEditing",
                    "ProjectId": "887dab06-da3f-4504-9511-5007bd7b591b",
                    "validation": {},
                    "apis": [
                        {
                            "path": "imageEditing/files",
                            "files": [
                                {
                                    "name": "file",
                                    "type": "image",
                                    "howGet": "file",
                                    "required": true
                                },
                                {
                                    "name": "mask",
                                    "type": "image",
                                    "howGet": "file",
                                    "required": true
                                }
                            ],
                            "method": [
                                "post"
                            ]
                        },
                        {
                            "path": "imageEditing/trackingFile/*",
                            "method": [
                                "get"
                            ]
                        },
                        {
                            "path": "imageEditing/checkServerHealth",
                            "method": [
                                "get"
                            ]
                        },
                        {
                            "path": "imageEditing/*",
                            "method": [
                                "get"
                            ]
                        }
                    ],
                    "queue": "null",
                    "type": "proxy",
                    "host": "192.168.33.21:3002/imageEditing/files",
                    "protocol": "http",
                    "rejectUnauthorized": false,
                    "createdAt": "2024-05-07T13:41:59.533Z",
                    "updatedAt": "2024-05-07T13:41:59.533Z"
                },
                "ApiKey": {
                    "id": "84d017d6-f239-40bf-ac7b-c76b86a281c9",
                    "value": "d2eac29aa79520571ff9bb031c72b790f27dce42d4e3bbd313e44fce72f40b10de351777f706f6ed55a49d636e612fee68d73f5fb32bc619e9e92cc096552ffc",
                    "UserId": "59176cea-f0ed-4cbe-b2ef-c57ecfb0c021",
                    "isValid": true,
                    "maxCount": 1000000,
                    "expireDate": "2029-10-28T13:44:03.000Z",
                    "createdAt": "2024-05-07T13:44:03.116Z",
                    "updatedAt": "2024-05-07T13:44:03.116Z"
                }
            }
        ]
    },
    "meta": {
        "requestId": "5afb142d-8fb6-486d-873f-0b32fe06200c"
    }
}
```
