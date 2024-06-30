import pika
import json

def on_request(ch, method, properties, body):
    try:
        message = json.loads(body.decode('utf-8'))
        if message["data"] == 'checkHealth':
            result = 'ok'
            response = {
                'result': result
            }
            ch.basic_publish(
                exchange='',
                routing_key=properties.reply_to,
                properties=pika.BasicProperties(
                    correlation_id=properties.correlation_id
                ),
                body=json.dumps(response)  # Convert the response to JSON
            )
            ch.basic_ack(delivery_tag=method.delivery_tag)
    except Exception as e:
        print(f"Error processing request: {e}")

def main():
    try:
        connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
        channel = connection.channel()

        queue = 'vision_image_segmentation_sgw'
        channel.queue_declare(queue=queue, durable=True)
        channel.basic_qos(prefetch_count=1)

        channel.basic_consume(queue=queue, on_message_callback=on_request)

        print(" [x] Awaiting RPC requests")
        channel.start_consuming()
    except Exception as e:
        print(f"Error during main: {e}")

if __name__ == '__main__':
    main()
