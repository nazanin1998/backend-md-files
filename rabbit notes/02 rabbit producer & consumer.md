# Rabbit Producer and Consumer

Source: https://roytuts.com/spring-boot-rabbitmq-producer-consumer-example/

## Introduction to RabbitMq
 
I will show you how to build producer consumer example using Spring Boot and RabbitMQ. This is also called __point-to-point messaging model__ as there are only one producer or sender and only one consumer or receiver.

* RabbitMQ is a message broker; it __accepts__, __stores__ and __forwards__ binary data or messages. 

* Many producers can send messages to a single queue and many consumers can consume messages from a single queue.

## Project Setup

1. In _pom.xml_ we must add following dependencies:
```
<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-amqp</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
</dependency>
```

2. In _application.properties_ add queue name as showed below:
```
queue.name=roytuts.queue
```
3. create producer class that it's resposible for sending messages.
```
import org.springframework.amqp.core.Queue;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class Producer {

	@Autowired
	private Queue queue;

	@Autowired
	private RabbitTemplate rabbitTemplate;

	public void sendMsg(final String msg) {
		rabbitTemplate.convertAndSend(queue.getName(), msg);
		System.out.println("Sent: " + msg);
	}

}
```

__Note__: _RabbitTemplate_ has _convertAndSend_ method that take two parameters. First take queue name and message.

4. Create Consumer class that it's responsible for receiving messages.
```
import org.springframework.amqp.rabbit.annotation.RabbitHandler;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

@Component
@RabbitListener(queues = "${queue.name}")
public class Consumer {

	@RabbitHandler
	public void receiveMsg(final String msg) {
		System.out.println("Received: " + msg);
	}

}
```

__Note__: The above class uses annotation @RabbitListener with queue name, so once a message arrives in the queue, the message will be consumed by the consumer.

5. Create Configuration class that configures beans for Queue, Producer and Consumer.

```
@Configuration
public class Config {

	@Value("${queue.name}")
	private String queueName;

	@Bean
	public Queue queue() {
		return new Queue(queueName);
	}

	// @Bean
	public Producer producer() {
		return new Producer();
	}

	// @Bean
	public Consumer consumer() {
		return new Consumer();
	}

}
```

6. Now for test, just send message as showed below:
```
producer.sendMsg("This message passes through RabbitMQ broker");
```