# Named Queries

Named queries are one of the core concepts in JPA. They enable you to declare a
query in your persistence layer and reference it in your business code. That makes
it easy to reuse an existing query.

When you define a named query, you can provide a JPQL query or a native SQL
query in very similar ways.

If we want to create a JPQL query, we have to annotate our entity with the
@NamedQuery annotation. If we want to create a SQL query, we have to annotate
our entity with the @NamedNativeQuery annotation.

### Steps to Define Named JPQL Query

If we want to create a JPQL query, we must follow these steps:

* Annotate the entity with the @NamedQuery annotation from JPA.
* Use @NamedQuery annotation’s name attribute to set name of the named query
(Product.findBySku)
* Use @NamedQuery annotation’s query attribute to set the JPQL query (SELECT p from
Product p WHERE p.sku =:sku) as the value
* Use named query name in a Repository

### Define Multiple Named JPQL Queries
If we want to create a multipleJPQL query, we must follow these steps:
* Annotate the entity with the @NamedQueries annotation from JPA/Hibernate.
* Use multiple @NamedQuery annotations from JPA/Hibernate to define a named queries
* Set the name of the named query as the value of the @NamedQuery annotation’s name
attribute.
* Set the JPQL query as the value of the @NamedQuery annotation’s query attribute.

Followings are sample codes for defining named queries. Here is the entity
class:

```
@Data
@Entity
@AllArgsConstructor
@NoArgsConstructor
@Accessors(chain = true)
@NamedQueries({
        @NamedQuery(
                name = "Customer.myFindByAge",
                query = "select c from Customer c where c.age = :age")
})
public class Customer {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    private String name;
    private int age;

    @DateTimeFormat(pattern = "yyyy-MM-dd")
    private LocalDate birthdate;

    @CreationTimestamp
    @Column(updatable = false)
    private LocalDateTime createdAt;
}
```

Here is repository interface:

```
public interface CustomerRepository extends JpaRepository<Customer, Long> {

    List<Customer> myFindByAge(@Param("age") int age);
}
```