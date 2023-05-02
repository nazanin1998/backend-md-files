# Creating Repository

In order to access the database with spring data JPA, you need to develop a repository for each entity.

## Interface
Following is the code for creating an interface for repository of the entity. Your interface should extend
_JpaRepository<ENTITY_NAME, ID_TYPE>_.

```
public interface CustomerRepository extends JpaRepository<Customer, Long> {
}
```


