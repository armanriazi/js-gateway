# Gateway

```bash
pnpm sequelize-cli model:generate --models-path ./src/models --env development --name TestUser --attributes firstName:string,lastName:string,email:string --migrations-path ./db/migrations

// --debug // --options-path // --url The database connection string to use

pnpm sequelize-cli db:migrate --config ./src/config/database.js --migrations-path ./db/migrations --to 20220729150928-create-user.js

pnpm sequelize-cli db:migrate:undo:all --config ./src/config/database.js --migrations-path ./db/migrations --to 20240415060456-create-test-user.js

pnpm sequelize-cli seed:generate --config ./src/config/database.js --seeders-path ./db/seeders --name User

pnpm sequelize-cli db:seed:all

pnpm sequelize-cli db:seed --config ./src/config/database.js --seeders-path ./db/seeders --seed 20240415064934-TestUser.js
```


# Gateway

## Getting Started 

### With docker

### Requirements

- rabbitMq
- redis
- postgres

Sample of docker compose:

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

After the first deployed 

```bash
npm run db:init
npm run db:migrate
npm run db:run:seed
```

## Without docker

```bash
git clone https://gitlab.partdp.ir/avanegar/avanegar-back.git
```

### Requirements

```bash
 npm install pm2@latest -g
```

Using postgres, redis
Docker-compose 

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

## Copy file sample.env and creatin .env


### Install packages

```bash
npm install
```

### Start to development


```bash
npm run db:init

npm run db:migrate

npm run db:run:seed

npm run dev

```

### When you done so far

```bash
npm run lint:fix
```

### Production mode

```bash
npm run db:init

npm run db:migrate

npm run db:run:seed

npm run start
```

### Test mode

```bash
npm run db:init

npm run db:migrate

npm run db:run:seed

npm run test or npm run test:watch
```

## Other commands

```bash
npm run db:migrate:generate
```

```bash
npm run db:migrate:undo
```

```bash
npm run db:create:seed
```

```bash
npm run db:undo:seed
```

## Adding app.py

```pyton
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

## Adding address file storage to env file

```bash
FILE_STORAGE_IP='192.168.33.72:8090'
```
