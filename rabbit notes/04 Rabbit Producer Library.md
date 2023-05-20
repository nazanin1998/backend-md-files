# RabbitMQ Producer Library

Our goal is creating a library for produce messages and everywhere we need a producer, add this library.

It has 2 steps:
1. Write a dependency that is aim to take _MessageRequest_ and send it __independent__ of send message technologis like _RabbitMq_, _Kafka_ and ... . This application calls __ProducerApplication__.

2. Write a dependency that is aim to send message by __RabbitMq__ tech. This application calls __RabbitProducerApplication__. Actually it will be an implementation for _ProducerApplication__ that use __Rabbit__ to send message.

## 1. Write Producer Application

_ProducerAPI_ is just an interface that will be injected in main application that wants to send message. 

It has one method named _sendAndreceive_ that takes two arguments: 
1. _RequestObject_ for send message parameter (class that defined for some base fields of request like headers, body, api params and ...)  
2. _Map<String, String>__ for destinationParams. As I said before, in this app we are independent of send message tech and for Rabbit message sending we need some parameters like queueName, routingkey and exchangeName but if we use Kafka we may need other parameters, so we use map of String and String to set key value on it and use them in implementation application.

```
public interface ProducerAPI {
    ResponseObject sendAndReceive(
        RequestObject message, 
        Map<String, String> destinationParams
    );
}
```

## 2. Write RabbitMQ Producer Application

This app should to implement interface of _ProducerApplication_. As we want to use this implementation in main application, use _@Primary_ on top of class. And use _@Service_ to make it injectable.

__Note__: The important problem for injecting this class from another application is that we have to initialize it's bean. _@SpringBootApplication_ can initialize all beans, but cause this app wil use as library so _@SpringBootApplication_ won't call anymore. For this reason we must use _@ComponentScan_ on main app to initialize all inner library's beans.

In implementation of _sendAndReceive_ first extract needed params like routingKey and exhangeName from destinationParams and then use _convertSendAndReceive_ method of rabbirTemplate to send mesage to specified routingKey and exchangeName.

For receiving reply we must cast it to responseObject or any other object that will reply by consumer app.

```
@Service
@AllArgsConstructor
public class ProducerAPIImpl implements ProducerAPI {

    private final RabbitTemplate rabbitTemplate;

    private static final String EXCHANGE = "EXCHANGE";
    private static final String ROUTING_KEY = "ROUTING_KEY";

    @Override
    public ResponseObject sendAndReceive(RequestObject message, Map<String, String> destinationParams) {

        String exchange = destinationParams.get(EXCHANGE);
        String routingKey = destinationParams.get(ROUTING_KEY);


        ResponseObject obj = (ResponseObject) rabbitTemplate.convertSendAndReceive(exchange, routingKey, message);
        return obj;

    }
}
```
__Casting has to important key in producer abd consumer sides:__

1. _RequestObject_ that will send and _ResponseObject_ that will receive should be serilizable. this means that they should specify on one central library that uses by producer app and consumer app. Because when it want to parse and convert from message to object, converter will compare its sender object package name and receiver object package name, and if they are not the same, it won't convert. So we need _sharedModelApp_.

2. Serializer and timeout should add as below:

```
@Configuration
public class RabbitProducerConfig {

    @Bean
    public MessageConverter converter() {
        return new Jackson2JsonMessageConverter();
    }

    @Bean
    public AmqpTemplate amqpTemplate(ConnectionFactory connectionFactory) {
        RabbitTemplate rabbitTemplate = new RabbitTemplate(connectionFactory);
        rabbitTemplate.setMessageConverter(converter());
        rabbitTemplate.setReceiveTimeout(60_000);
        rabbitTemplate.setReplyTimeout(60_000);
        return rabbitTemplate;
    }
}
```
