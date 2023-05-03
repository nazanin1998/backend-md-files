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
* CascadeType.MERGE - update() operation cascades to the related entities.
* CascadeType.REFRESH
* CascadeType.REMOVE - delete() operation cascades to the related entities.
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

### Important Attention
You have to be so careful with edit process of relational entities. Imagine you have the 
following object, which you desire to update:

```
{
    "id": 102,
    "name": "Reza Azad",
    "age": 33,
    "birthdate": "1990-05-31",
    "createdAt": "2023-05-01T12:12:27.0318943",
    "customerAddress": {
        "id": 104,
        "country": "IRI",
        "province": "Tehran province",
        "city": "Tehran City",
        "street": "Ashrafi St",
        "allay": "kooche delgosha allay",
        "number": "654 ",
        "floor": "5",
        "zipcode": "45878778545"
    }
}
```

Then if you set _id_ of the customerAddress to _null_ or any value except _104_, then
a new record will be inserted to customerAddress table. __This is a Hack!__. Also, if 
you put an existing id in customerAddress instead of _104_, then you would __lose__ 
one-to-one relation. 

#### Solution
For preventing these situations, we can designate an exclusive DTO for edit process.
Then with custom validators we can check existence of primary keys of the source
object. Then we should check the equality of foreign keys with the values in the
received DTO.

### Sample JSON for inserting new record

Here is a sample code to insert relational entities to the database by post method.

```
{
    "id": null,
    "name": "ali",
    "age": 28,
    "birthdate": "1998-05-31",
    "customerAddress":{
        "id":null,
        "country":"Italy",
        "province":"emilia romania",
        "city":"bologna",
        "street":"via sandonato",
        "allay":"33",
        "number":"654",
        "floor":"5",
        "zipcode":"45878778545"
    }
}
```


