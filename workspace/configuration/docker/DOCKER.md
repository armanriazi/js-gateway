
### Requirements

- [x] RabbitMq
- [x] Redis
- [x] Postgres

Sample of docker-compose

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

Run commands after the first deployment:

‍‍‍‍‍‍```bash
npm run db:init
npm run db:migrate
npm run db:run:seed
‍‍‍```

## Get-start with Docker

Copy of repo:

```bash
git clone https://gitlab.partdp.ir/avanegar/avanegar-back.git
```

### Requirements

Instamm pm2

```bash
 npm install pm2@latest -g
```

Install Redis & Postgres

```yaml
version: '3'
services:
    postgres:
        restart: always
        image: postgres
        container_name: postgres
        volumes:
          - ./postgres-data:/var/lib/postgresql/data
          - /home/Projects/recommenderengine-back/assets/uploads:/home/Projects/
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

Copy of file sample env to a new file .env

### Instal packages

```bash
npm install
```

### Start to develop code

```bash
npm run db:init
npm run db:migrate
npm run db:run:seed
npm run dev
```

### When changes occured

```bash
npm run lint:fix
```

### Production stage

```bash
npm run db:init
npm run db:migrate
npm run db:run:seed
npm run start
```

### Test stage

```bash
npm run db:init
npm run db:migrate
npm run db:run:seed
npm run test or npm run test:watch
```

## Others commands

Create a new migrate:

```bash
npm run db:migrate:generate
```

Rest mode:

```bash
npm run db:migrate:undo
```

Create seeder data

```bash
npm run db:create:seed
```

Revert to the initiated data

```bash
npm run db:undo:seed
```

## Add app.py

```python
import requests
import os
from pathlib import Path
def get_file(data):
    try:
        url = "http://{FILE_STORAGE_IP}/download/{filePath}".format(
                FILE_STORAGE_IP=os.environ.get('FILE_STORAGE_IP'), filePath=data["filePath"])
        newFilePath = "assets/uploads/"+data["filePath"].split("/")[-1]
        response = requests.get(url)
        if response.status_code == 200:
            Path("./assets/uploads").mkdir(parents=True, exist_ok=True)
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

## Adding storage url to the env

```javascript
FILE_STORAGE_IP='192.168.33.72:8090'
```