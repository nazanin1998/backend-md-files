# One To One Mapping

Here we will introduce one to one mapping in spring data jpa. 

## How it's done?

In order to build a one-to-one relationship between two entities, follow the next steps.

1. Add the associate entity as a field of the source entity.
2. Annotate the added field with the following annotations.
    ```
    @OneToOne(cascade = CascadeType.ALL)
    @JoinColumn(referencedColumnName = "id")
    ```

### JPA Supported Cascade Types

* CascadeType.PERSIST - save() and persist() operations cascade to related entities.
* CascadeType.MERGE - 
* CascadeType.REFRESH
* CascadeType.REMOVE - delete() operation cascades to related entities.
* CascadeType.DETACH
* CascadeType.ALL - All of the above ones are shorthanded in this item.

Cascade type is defined as input of __@OneToOne__ annotation. Like:
```
@OneToOne(cascade = CascadeType.ALL)
```

### Example for source and associate entities

Here we present a sample code for source class in a one-to-one relation.

```
@Data
@Entity
@AllArgsConstructor
@NoArgsConstructor
@Accessors(chain = true)
@NamedQueries({
        @NamedQuery(
                name = "Customer.myFindByAge2",
                query = "select c from Customer c where c.age = :age order by c.name asc")
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

    @OneToOne(cascade = CascadeType.ALL)
    @JoinColumn(referencedColumnName = "id")
    private CustomerAddress customerAddress;
}
```

Here is sample code for the associated entity.

```
@Data
@AllArgsConstructor
@NoArgsConstructor
@Accessors(chain = true)
@Entity
public class CustomerAddress {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    private String country;
    private String province;
    private String city;
    private String street;
    private String allay;
    private String number;
    private String floor;
    private String zipcode;
}
```


