# Spring Data JPA Configuration
 
Here we persent the configuration needed for adding spring data jpa. 

## Creating Database (Schema)
For the first step you need to create a database (schema) in your data store. For this means open MySQL Workbench software and execute the following line. Imaging the name of the database will be ecommerce.

```
create database ecommerce
```

## Dependencies
In order tot connect to MySQL database you need the following dependeicies.
* Spring Web
* Spring Data JPA
* MySQL Driver

## Configurations
Following are necessary configurations for connecting the project to the MySQL database.
You need to define the database url with the options, username, password and data defination language (DDL) mode.

```
spring.datasource.url=jdbc:mysql://localhost:3306/SCHEMA_NAME?useSSL=false
spring.datasource.username=root
spring.datasource.password=PASSWORD_FOR_ROOT_URSER

spring.jpa.hibernate.ddl-auto=update

spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5Dialect
```

Available options for ddl mode are:
* __none__: hibernate is not going to create table for you. you have to create all tables manually
* __create-only__: tells hibernate to generate the db schema from the entity model.
* __create__: tells hibernate to drop the db schema and recreate it afterward using the entity model as a reference.
* __drop__: tells hibernate to drop the db schema using the entity model as a reference for the DDL DROP statements.
* __create-drop__: combine create and drop toghter.
* __validate__: just validate the underlying db schema against the entity mapping .
* __update__: update the existing schema based on entity mapping.

To show all the __sql statements log__ write the first line. and the second line format all these logs.
```
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```
# Project Structure
1. First you should create new package named _domain_ or _entity_ or _model_ to put all JPA entities in it.
2. Second create _repository_ package to access database.

## Create Domain
Here we consider creating a sample domain, which will be mapped to a table in the database.

Use __@Entity__ annotaion that imported from __jakarta.persistence__ to specify that this class is JPA entity.
And for each entity we need __primary key__, so use __@Id__ annotaion for id variable.

```
import jakarta.persistence.Entity;
import jakarta.persistence.Id;

@Data
@Entity
@NoArgsConstructor
@AllArgsConstructor
@Accessors(chain = true)
public class Customer {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    private String name;
    private int age;
}
```
Now if you run project and _ddl-auto_ parameter of _application.properties_ has been set to _update_, Product table will create automatically in MySQL.


## Create Repository
In order to access the database you need to create a repository for each created domain. Following is repository for the above domain.

```
public interface CustomerRepository extends JpaRepository<Customer, Long> {
}
```

## Create service
Now you can access the databse wiht a service layer. The following offers a method to add an entity to the database.

```
@Service
@AllArgsConstructor
public class CustomerService {

    private final CustomerRepository customerRepository;

    public Customer addCustomer(Customer customer) {

        return customerRepository.save(customer);
    }

}
```

## Test
Following is how you can write tests for JPA repositories.

```
import static org.assertj.core.api.AssertionsForClassTypes.assertThat;

@DataJpaTest
@TestMethodOrder(MethodOrderer.OrderAnnotation.class)
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
public class JpaRepositoryTests {

    @Autowired
    CustomerRepository customerRepository;

    @Order(1)
    @Test
    void testBookRepository() {

        Long countBefore = customerRepository.count();
        customerRepository.save(new Customer().setAge(200));
        Long countAfter = customerRepository.count();

        assertThat(countBefore).isLessThan(countAfter);
    }

}
```



