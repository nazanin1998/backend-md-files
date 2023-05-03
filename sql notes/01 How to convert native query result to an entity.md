# How to convert the result of a native query to an entity

When you have a complicated query you need to implement it with the native code. 
Therefore, you have to be able to convert the result of the search query to a 
model.

### MySQL query
Here is the query which we can perform the search. This query returns the id and name
of the _University_ along with name of the _Department_ with their aliases.

```
select ecommerce.university.id as uniId, ecommerce.university.name as uniName, ecommerce.department.name as depName 
from ecommerce.university
join ecommerce.department
on ecommerce.university.id = ecommerce.department.university_id
where ecommerce.department.name in ('Computer') 
```
Following is a sample result for the above query.

| uniId | uniName | depName  |
|:------|:--------|:---------|
| 2     | Alzahra | Computer |

### 1. Creating the result entity
In order to have the results mapped, we need to define an entity, which has 
fields to keep the mapped result.

We need to define a mapper, which maps the query result to the entity. For this
matter, we use __@SqlResultSetMapping__ annotation. This annotation get two 
parameters:
* __name__: This is the mapper name. This name is used as in the named query.
* __entities__: Gets _@EntityResult_. This annotation has gets to parameter:
  * __entityClass__": This the name of the result entity.
  * _fields__: Here we define the name of the fields of the result entity and
  the corresponding sql column names.

```
@Data
@AllArgsConstructor
@NoArgsConstructor
@Accessors(chain = true)
@Entity
@SqlResultSetMapping(
        name = "CustomMapping1",
        entities = @EntityResult(
                entityClass = resultEntity.class,
                fields = {
                        @FieldResult(name = "id", column = "uniId"),
                        @FieldResult(name = "universityName", column = "uniName"),
                        @FieldResult(name = "departmentName", column = "depName"),
                }
        )
)
public class resultEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    private String universityName;
    private String departmentName;
}

```


### 2. Creating Entities
First we need to define the relational entities. Here we have a one-to-many relationship
between _University_ and _Department_ entities.

Here are the code for _University_ entity. Kindly, pay attention to the _resultClass_
and _resultSetMapping_ of the _@NamedNativeQuery_ annotation. The _resultClass_ is the
result entity and the _resultSetMapping_ is the name, which is set the 
-@ResultSetMapping- annotation.

```
@AllArgsConstructor
@NoArgsConstructor
@Accessors(chain = true)
@Entity
@NamedNativeQueries({
        @NamedNativeQuery(
                name = "University.findUniversitiesByDepartments",
                query = "select ecommerce.university.id as uniId, ecommerce.university.name as uniName, ecommerce.department.name as depName \n" +
                        "from ecommerce.university\n" +
                        "join ecommerce.department\n" +
                        "on ecommerce.university.id = ecommerce.department.university_id\n" +
                        "where ecommerce.department.name in :dname",
                resultClass = resultEntity.class,
                resultSetMapping = "CustomMapping1"
        )
})

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

Here is the Department class code:

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

### 3. Repository for _University_ entity
Here is the code for _University_ entity.

```
public interface UniversityRepository extends JpaRepository<University, Long> {

    List<resultEntity> findUniversitiesByDepartments(@Param("dname") List<String> departmentNames);
}
```

### 4. Service method
Here is the service method for calling the repository method.

```
@Service
@AllArgsConstructor
public class UniversityService {

    private final UniversityRepository uniRepo;

    public List<resultEntity> findByNativeQuery(List<String> departmentNames) {

        return uniRepo.findUniversitiesByDepartments(departmentNames);
    }
}
```

### 5. Controller class

Here is the controller class to use the service class.

```
@RestController
@AllArgsConstructor
@RequestMapping("/university")
public class UniversityController {

    private final UniversityService universityService;
    
    @GetMapping("/test")
    public ResponseEntity<?> test(
            @RequestParam List<String> departmentNames
    ) {

        return ResponseEntity.ok(universityService.findByNativeQuery(departmentNames));
    }
}
```


