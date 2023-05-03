# One-to-Many and Many-to-One

## Unidirectional
In a relational database system, o one-to-many association links
two tables based on a Foreign key column. __In this case, the child
table record references the primary key of the parent table row__.

The unidirectional @OneToMany association is simpler since it is just
the parent side that defines the relationship. In unidirectional mapping
we use only @OneToMany annotation. Kindly pay attention to the name field in 
@JoinColumn(...) for departments field.

### Source and Target Entities
Here is the code for source entity.

```
@Data
@AllArgsConstructor
@NoArgsConstructor
@Accessors(chain = true)
@Entity
public class University {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    private String name;

    @OneToMany(cascade = CascadeType.ALL, fetch = FetchType.EAGER)
    @JoinColumn(name = "university_id", referencedColumnName = "id")
    private Set<Department> departments = new HashSet<>();
}
```

Following is the code for the target entity.

```
@Data
@AllArgsConstructor
@NoArgsConstructor
@Accessors(chain = true)
@Entity
public class Department {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    private String name;

}
```

### JSON For Post Method
Here is a sample JSON for inserting a record of aforementioned entities to the database.

```
{
    "id":null,
    "name":"Polytechnic",
    "departments": [
        {
            "id":null,
            "name":"chemistry"
        },
        {
            "id":null,
            "name":"Aerospace"
        }
    ]
}
```

## Default Fetch Types
* OneToOne: EAGER
* OneToMany: Lazy
* ManyToOne: Eager
* ManyToMany: Lazy
 

## Bidirectional

The bidirectional association requires the child entity mapping to 
provide a @ManyToOne annotation, which is responsible for controlling 
the association. In bidirectional mapping we use both @OneToMany and 
@ManyToOne annotations.

