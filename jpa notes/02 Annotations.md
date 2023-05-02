# JPA Annotations

JPA has lots of annotations. Here we will cover most useful of them

## @Entity
By putting this annotation over a class, JPA will make a table according to the class variables in the database. Automatically, the name of the table will be lower case and combined words will be separated by dash.

## @Id 
This annotation is necessary for an entity to define its primary key.

## @GeneratedValue(strategy = GenerationType.AUTO)
This annotation will make the primary key to be produced automatically.

## @Column(unique = true)
By this means you can make a field of entity unique.

## @Column(nullable = false)
By this means you can make a field of entity not nullable.

## @Column(updatable = false)
This annotation will prevent the field from updating.

## @CreationTimestamp
This annotation can be used for automatic update of a timestamp i.e. createdAt field. _@Column(updatable = false)_ annotation will prevent the update of the field
```
@CreationTimestamp
@Column(updatable = false)
private LocalDateTime createdAt;
```

## @UpdateTimestamp
This annotation will automatically update the timestamp field, whenever the entity is being updated
```
@UpdateTimestamp
private LocalDateTime modifiedAt;
```

## @CreatedDate
This annotation is another one to automatically update the timestamp, while inserting the entity to the database. But you should remember you need to configure audit aware bean.
Following is how you can use this annotation.
```
@CreatedDate
@Column(updatable = false)
private LocalDateTime createdAt;
```
Here is how you can develop a custom aware class

```
public class CustomAuditorAware implements AuditorAware<String> {

    @Override
    public Optional<String> getCurrentAuditor() {
        //String loggedName = SecurityContextHolder.getContext().getAuthentication().getName();
        return Optional.of("reza");
    }

}
```
Following is the code for injecting bean to the spring context.
```
@Configuration
@EnableJpaAuditing
public class AuditorConfig {

    @Bean
    public CustomAuditorAware auditorProvider(){
        return new CustomAuditorAware();
    }
}
```

**Attention**: Also, you have to put _@EntityListeners(AuditingEntityListener.class)_ above the target entity class. Otherwise the created date field will become null.

## @LastModifiedDate
This is another annotation for updating the modified date of the entity. Like _@CreatedDate_ you need to configure the audit aware bean.

