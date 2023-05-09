# Rabbit Object Produce and Consume

Our goal is sending object instead of primative data types.

For example you want to send below object to consumer to register a user with.

```
{
    "age": 20,
    "marriageStatus": false,
    "username": "nazaninfz",
    "password": "1233"
}
```
## 1. Producer Application
1. Create entity class named _RegisterRequest_ as showed below:
```
@Data

public class RegisterRequest {

    private String username;
    private String password;
    private int age;
    private boolean marriageStatus;
}
```
2. Create _configuration_ class to create _queue_, _exchange_ and _binding_ and save them on bean.
 
__Note__: Write converter that returns _Jackson2JsonMessageConverter_ to automatically handle JsonParse process. We set that converter by _setMessageConverter_ method. 

```
@Configuration
@Getter
public class RabbitConfiguration {

    @Value("${rabbit.queue.name}")
    private String queueName;

    @Value("${rabbit.exchange.name}")
    private String exchangeName;

    @Value("${rabbit.routing.key}")
    private String routingKey;

    @Bean
    public Queue createQueue() {
        return new Queue(queueName, false);
    }

    @Bean
    TopicExchange createExchange() {
        return new TopicExchange(exchangeName);
    }

    @Bean
    Binding createBinding(Queue queue, TopicExchange exchange) {
        return BindingBuilder.bind(queue).to(exchange).with(routingKey);
    }

    @Bean
    public MessageConverter converter(){
        return new Jackson2JsonMessageConverter();
    }

    @Bean
    public AmqpTemplate amqpTemplate(ConnectionFactory connectionFactory){
        RabbitTemplate rabbitTemplate = new RabbitTemplate(connectionFactory);
        rabbitTemplate.setMessageConverter(converter());
        return rabbitTemplate;
    }

}
```

3. Create _service_ class to send message.
```
@Service
@AllArgsConstructor
public class ProduceMessageService {

    private final RabbitTemplate rabbitTemplate;
    private final RabbitConfiguration rabbitConfiguration;

    public String sendRegisterCommandToConsumer(RegisterRequest body){
        rabbitTemplate.convertAndSend(
                rabbitConfiguration.getExchangeName(),
                rabbitConfiguration.getRoutingKey(),
                body
        );
        return "Message(" + body + ")" + " has been produced.";
    }

}
```

## 2. Consumer Application

1. Create request entity just as you made on Producer app. cause you need that to receive message and identify that.
 
2. In _configuration_ class just write converter and set it as we explained previously. you dont need queue data anymore.

3. Create service class that listen on queue by it's name.
```
@Service
public class ConsumerService {

    @RabbitListener(queues = {"${rabbit.queue.name}"})
    public void consumeMessage(RegisterRequest messageBody) {
        System.out.println("Consumed message:" +messageBody);
    }

}
```