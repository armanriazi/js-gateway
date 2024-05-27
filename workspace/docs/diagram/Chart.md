## Chart

```mermaid
flowchart RL
   Client == GRPC | WS | Http ==> Gateway
   Gateway == AMQP ==> Rabbitmq
   Rabbitmq <--> AIService1
   Rabbitmq <--> AIServiceN
```
