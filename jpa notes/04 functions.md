# Spring Data JPA Methods

Here we will discover functions related to repository.

## Repository Methods

Following is a list of famous methods related to spring data jpa.
* save()
* findById()
* saveAll()
* findAll()
  * findAll() method can be used with sorting. The following code shows
  how you can use this method with sort.
  ```
  public List<Customer> findAll() {

        Sort sort = Sort.by("name").descending();
        return customerRepository.findAll(sort);
    }
  ```
* deleteById(). This method performs two queries, first select and then delete
* deleteAll()
* delete(). This method performs only one query, which is delete.
* count()
* existById()

## Query Methods

Spring boot JPA can interpret method name to queries.

### Rules to create query methods
1. Method name should start with one of the following prefixes, which are called
__subject keywords__:
   * find...By
   * read...By
   * query...By
   * count...By
   * get...By
   * exist...By
   * delete...By
   * search...By
   * Stream...By   
2. If you want to limit the  number or returned query results, we can add __First__
and __Top__ keyword before the first by word i.e. :
    * findFirstByName
    * readFirst2ByName
    * FindTop10ByName

3. If you want to select unique results, you have to add __distinct__ keyword
before the first by word, like:
    * findDistinctByName
    * findNameDistinctBy

4. Combine property expressions with __And__ and __Or__, like:
    * findByNameAndDescription
    * findByNameOrDescription

### Returning Values From Query Methods
A query method can return only one result or more than one result.
1. if we are writing a query that should return only one result, we can return 
the following types:
   * __Basic type__. Our query method will return the found basic type or null.
   * __Entity__. Our query method will return an entity object or null.
   * __Guava / Java 8 Optional<T>__. Our query method will return an Optional that contains the found
   object or an empty Optional.
   
2. if we are writing a query method that should return more than one result, we can return the
   following types:
   * __List<T>__. Our query method will return a list that contains the query results or an empty list.
   * __Stream<T>__. Our query method will return a Stream that can be used to access the query results or
   an empty Stream.


### Query predicate keywords

Here is a complete list of query predicate keywords.


| Logical keyword                      | Keyword expressions                      |
|:-------------------------------------|:-----------------------------------------|
| AND                                  | And                                      |
| OR                                   | Or                                       |
| AFTER                                | After, IsAfter                           |
| BEFORE                               | Before, IsBefore                         |
| CONTAINING                           | Containing, IsContaining, Contains       |
| BETWEEN                              | Between, IsBetween                       |
| ENDING_WITH                          | EndingWith, IsEndingWith, EndsWith       |
| EXISTS                               | Exists                                   |
| FALSE                                | False, IsFalse                           |
| GREATER_THAN                         | GreaterThan, IsGreaterThan               |
| GREATER_THAN_EQUALS                  | GreaterThanEqual, IsGreaterThanEqual     |
| IN                                   | In, IsIn                                 |
| IS                                   | Is, Equals, (or no keyword)              |
| IS_EMPTY                             | IsEmpty, Empty                           |
| IS_NOT_EMPTY                         | IsNotEmpty, NotEmpty                     |
| IS_NOT_NULL                          | NotNull, IsNotNull                       |
| IS_NULL                              | Null, IsNull                             |
| LESS_THAN                            | LessThan, IsLessThan                     |
| LESS_THAN_EQUAL                      | LessThanEqual, IsLessThanEqual           |
| LIKE                                 | Like, IsLike                             |
| NEAR                                 | Near, IsNear                             |
| NOT                                  | Not, IsNot                               |
| NOT_IN                               | NotIn, IsNotIn                           |
| NOT_LIKE                             | NotLike, IsNotLike                       |
| REGEX                                | Regex, MatchesRegex, Matches             |
| STARTING_WITH                        | StartingWith, IsStartingWith, StartsWith |
| TRUE                                 | True, IsTrue                             |
| WITHIN                               | Within, IsWithin                         |                       

### Query predicate modifier keywords

| Keyword                        | Description                                                                                                         |
|:-------------------------------|:--------------------------------------------------------------------------------------------------------------------|
| IgnoreCase, IgnoringCase       | Used with a predicate keyword for case-insensitive comparison.                                                      |
| AllIgnoreCase, AllIgnoringCase | Ignore case for all suitable properties. Used somewhere in the query method predicate.                              |
| OrderBy…                       | Specify a static sorting order followed by the property path and direction (e. g. OrderByFirstnameAscLastnameDesc). |


### Query method - find by single field name

Here is an example of how we can find entities by single field name:

```
public interface CustomerRepository extends JpaRepository<Customer, Long> {

   List<Customer> findByName(String name);

}
```

The equivalent SQL is like(the schema name and table name is ecommerce and customer):

```
select * from ecommerce.customer where name = "YOUR-DISIRED-NAME";
```

### Query methods - find by multiple fields

You can perform search by multiple fields with the following keywords: __And__ and __Or__.
For example:

```
public interface CustomerRepository extends JpaRepository<Customer, Long> {
    
    List<Customer> findByNameAndAge(String name, int age);
    List<Customer> findCustomerByNameOrAge(String name, int age);
}
```

### Find distinct names of the customers

Following is code snippet to find distinct names of the customers.

```
public interface CustomerRepository extends JpaRepository<Customer, Long> {

    @Query("select distinct c.name from Customer c")
    List<String> findDistinctCustomerNames();
}

```

### Query methods - Find by distinct

This method retrieves entities with distinct field as the input. I did not understand
the usage of this method. If you want to get distinct values of a column you can use the
previous method.

```
public interface CustomerRepository extends JpaRepository<Customer, Long> {

    List<Customer> findDistinctByName(String name);
}

```

### Query methods - Find by Greater/Less Than (Equal)

The following is an example of how we can query for entities using value comparison.

```
public interface CustomerRepository extends JpaRepository<Customer, Long> {

    List<Customer> findCustomerByAgeGreaterThanEqual(int ageThreshold);

}
```

### Query methods - find by containing

If we want to query by a portion of a field then we can use __Containing__ query predicate.
Here we want to find entities with a given name-portion.

```
public interface CustomerRepository extends JpaRepository<Customer, Long> {

    List<Customer> findCustomerByNameContainingIgnoreCase(String namePortion);
}
```

Following is the equivalent MySQL query:

```
select * 
from ecommerce.customer
where name like "%NAME_PORTION%";
```

### Query Methods - find by like

Find by like is the same as find by containing but with wildcards: _%INPUT_PORTION%_.
Here is a sample code for repository layer:

```
public interface CustomerRepository extends JpaRepository<Customer, Long> {

    List<Customer> findByNameLike(String namePortion);
}
```

The following is code for controller part

```
GetMapping("/find-by-name-like")
public ResponseEntity<?> findByNameLike(
      @RequestParam String namePortion
) {

  return ResponseEntity
          .status(HttpStatus.OK.value())
          .body(customerService.findByNameLike("%" +  namePortion + "%"));
}
```
