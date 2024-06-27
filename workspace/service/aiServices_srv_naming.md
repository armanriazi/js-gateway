## Naming Service APIs and Routing

### SRT

To use SRT services, 'srt' must be appended to the end of the service name, just as operations will be performed on it in the code section.

‍‍‍‍‍```javascript
req.srtProxy = serviceName.toLowerCase().includes('srt')
```

### File

The program realizes that the requested API address is intended for a file when it finds the names listed below in the specified path within this part of the address.


`ip:port/service/imageEditing/{{X}}/ok`

like:

```md
‍ip:port/service/imageEditing/file/ok
‍ip:port/service/imageEditing/files/ok
‍ip:port/service/imageEditing/mp3/ok
```

#### ServiceName

‍ip:port/service/keywordsExtraction/files/ok`

#### Main code

‍‍‍‍‍```javascript
const isFileRequest = (absolutePath, serviceName) => {
  return (
    absolutePath.toLowerCase().includes('mp3') ||
    absolutePath.toLowerCase().includes('file') ||
    serviceName === 'keywordsExtraction'
  )
}
```
