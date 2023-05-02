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
Following are necessary cofigurations for connecting the project to the MySQL database.
You need to define the database url with the options, username, password and data defination language (DDL) mode.

```
spring.datasource.url=jdbc:mysql://localhost:3306/SCHEMA_NAME?useSSL=false
spring.datasource.username=root
spring.datasource.password=PASSWORD_FOR_ROOT_URSER

spring.jpa.hibernate.ddl-auto=update

spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5Dialect
```

## Create Domain
Here we cosider creating a sample domain, which will be mapped to a table in the database.

```
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



