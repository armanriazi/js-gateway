

### نیازمندی ها

- rabbitMq
- redis
- postgres

نمونه داکر کامپوز :

```yaml
version: '3'
services:
    postgres:
        restart: always
        image: postgres
        container_name: postgres
        network_mode: host
        volumes:
          - ./postgres-data:/var/lib/postgresql/data
        environment:
          - POSTGRES_USER=postgres
          - POSTGRES_PASSWORD=postgres
          - POSTGRES_DB=postgres
    rabbitmq:
        restart: always
        image: rabbitmq
        container_name: rabbitmq
        network_mode: host
        healthcheck:
            test: ["CMD", "rabbitmqctl", "status"]
            interval: 5s
            timeout: 10s
            retries: 3
    redis:
        restart: always
        command: redis-server --appendonly yes --requirepass "SBFo0'rR9LpqY5%GZiZp"
        image: redis
        container_name: redis
        network_mode: host
    api:
        build: .
        depends_on:
          - postgres
          - rabbitmq
          - redis
        env_file:
          - ./.env
        restart: on-failure
        network_mode: host
        container_name: api

```

پس از اولین دیپلوی ذیل دستورات باید وارد شود:

‍‍‍‍‍‍```bash

npm run db:init
npm run db:migrate
npm run db:run:seed

‍‍‍```

##  (بدون ایمیج داکر) شروع به کار <a name = "getting_started"></a>
ابتدا مخزن کدها را با دستور زیر کپی کنید:

```bash
git clone https://gitlab.partdp.ir/avanegar/avanegar-back.git
```
### نیازمندی‌ها

نصب pm2

```bash
 npm install pm2@latest -g
```

استفاده از postgres, redis
با استفاده از داکر کامپوز

```yaml
version: '3'
services:
    postgres:
        restart: always
        image: postgres
        container_name: postgres
        volumes:
          - ./postgres-data:/var/lib/postgresql/data
          - /home/Projects/recommenderengine-back/uploads:/home/Projects/
        environment:
          - POSTGRES_USER=
          - POSTGRES_PASSWORD=
          - POSTGRES_DB=
        network_mode: host
    redis:
        restart: always
        image: redis
        container_name: redis
        ports:
            - 6379:6379
        network_mode: host
        deploy:
          resources:
            limits:
              memory: 1G
```

## کپی کردن فایل sample.env و ساخت فایل env.


### نصب پکیج‌ها

```bash
npm install
```

### شروع توسعه

```bash
npm run db:init
npm run db:migrate
npm run db:run:seed
npm run dev
```

### وقتی تغییرات انجام شد

```bash
npm run lint:fix
```

### در حالت پروداکشن

```bash
npm run db:init

npm run db:migrate

npm run db:run:seed

npm run start
```

### در حالت تست

```bash
npm run db:init

npm run db:migrate

npm run db:run:seed

npm run test or npm run test:watch
```

## سایر دستورات

ساخت یک مایگریت جدید

```bash
npm run db:migrate:generate
```

برگشتن به حالت اولیه ماگریت ها

```bash
npm run db:migrate:undo
```

ساخت دیتای اولیه

```bash
npm run db:create:seed
```

برگشتن به حالت اولیه دیتاها

```bash
npm run db:undo:seed
```

## اضافه کردن به app.py

```python
import requests
import os
from pathlib import Path
def get_file(data):
    try:
        url = "http://{FILE_STORAGE_IP}/download/{filePath}".format(
                FILE_STORAGE_IP=os.environ.get('FILE_STORAGE_IP'), filePath=data["filePath"])
        newFilePath = "uploads/"+data["filePath"].split("/")[-1]
        response = requests.get(url)
        if response.status_code == 200:
            Path("./uploads").mkdir(parents=True, exist_ok=True)
            with open(newFilePath , "wb") as file:
                file.write(response.content)
            data["filePath"] = newFilePath
            return data
        else:
            raise Exception("download failed")
    except Exception as error:
        raise error

data = get_file(data)
```


## اضافه کردن ادرس فایل استوریج به env


```javascript
FILE_STORAGE_IP='192.168.33.72:8090'
```
