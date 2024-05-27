# نامگذاری سرویس و مسیر آدرس APIs

## SRT

جهت استفاده از سرویس های نوع srt میبایست به آخر نام سرویس srt افزوده شود کما اینکه در بخش کد قرار است عملیاتی روی ان انجام شود.


‍‍‍‍‍```javascript
req.srtProxy = serviceName.toLowerCase().includes('srt')
```

## File

برنامه زمانی متوجه می شود که آدرس Api درخواستی مورد تقاضا برای فایل است که در مسیر مشخص شده ذیل اسامی ذیل در این بخش از ادرس موجود باشد.


> `ip:port/service/imageEditing/{{X}}/ok`

> like

```
‍ip:port/service/imageEditing/file/ok

‍ip:port/service/imageEditing/files/ok

‍ip:port/service/imageEditing/mp3/ok

```

ServiceName

> `‍ip:port/service/keywordsExtraction/files/ok`

> Main code

‍‍‍‍‍```javascript
const isFileRequest = (absolutePath, serviceName) => {
  return (
    absolutePath.toLowerCase().includes('mp3') ||
    absolutePath.toLowerCase().includes('file') ||
    serviceName === 'keywordsExtraction'
  )
}
```
