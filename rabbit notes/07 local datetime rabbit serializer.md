# LocalDateTime RabbitMQ Serializer

Send an receive object and primative data types are fine, but if we want to send object that includes localDateTime, an _conventionMessageException_ will occure. 

For fix this problem and being able to send LocalDateTime you should add _@JsonSerialize_ and _@JsonDeserialize_ to the top thevcariable, And add following dependency to the _pom.xml_ file.

```
<dependency>
    <groupId>com.fasterxml.jackson.datatype</groupId>
    <artifactId>jackson-datatype-jsr310</artifactId>
</dependency>
```

```
 @JsonSerialize(using = LocalDateTimeSerializer.class)
    @JsonDeserialize(using = LocalDateTimeDeserializer.class)
    private LocalDateTime requestDate;
```
And add following bean to configuration file and set it to primary bean.
The important part is registering _JavaTimeModule_ and disable serializer feature.

```
@Bean
@Primary
public ObjectMapper objectMapper() {
        ObjectMapper mapper = new ObjectMapper();
        mapper.registerModule(new JavaTimeModule());
        mapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS);
        return mapper;
}
``