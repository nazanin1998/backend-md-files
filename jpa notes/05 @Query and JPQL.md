# @Query and JPQL

Let's investigate @Query annotation and JPQL.

## @Query

### Problem with Query Methods

Here are some problems related to query methods.

* __Keyword support__ - If the method name parser doesn’t support the required keyword,
we cannot use this strategy.
* The method names of complex query methods are long and ugly.

```
List<Product> findByDescriptionContainsOrNameContainsAllIgnoreCase(String description, String name);
```

* And this is for just two parameters. What happens when you want to create a query
for 5 parameters?

This is the point when you'll most likely want to prefer to write your own queries.
This is doable via the @Query annotation.

### Understanding @Query Annotation
* We can configure the invoked database query by annotating the query method with
the @Query annotation

```
@Query("select c " +
        "from Customer c " +
        "where c.name = ?1 and c.age > ?2")
List<Customer> findByNameAndAge2(String name, int age);
```

* We can use the @Query annotation in Spring Data JPA to execute both JPQL and
native SQL queries
* No need to follow query method naming conventions


## JPQL

JPQL stands for the Java Persistence Query Language. It is defined in the JPA
specification and is an object-oriented query language used to perform
database operations on persistent entities.

Hibernate, or any other JPA implementation, has to transform the JPQL query
into SQL. The syntax of a JPQL FROM clause is similar to SQL but
uses the __entity model instead of table or column names__.

### JPQL Features

* It is a __platform-independent__ query language.
* It is simple and robust.
* It can be used with __any__ type of relational database.
* It can be declared statically into metadata or can also be dynamically built in
code.
* It is __case-insensitive__.

If your database can change or varies from development to production, as long
as they're both relational - JPQL works wonders, and you can write JPQL
queries to create __generic logic__ that can be used over and over again.


### Creating JPQL Queries

Steps to create JPQL query with the @Query annotation:

* __Step 1__: Add a query method to your repository interface.
* __Step 2__: Annotate the query method with the @Query annotation, and specify the
invoked query by setting it as the value of the @Query annotation.


### JPQL Query with Index (Position) Parameters

When using position-based parameters, you have to keep track of the order in
which you supply the parameters in:

```
@Query("select c " +
        "from Customer c " +
        "where c.name = ?1 and c.age > ?2")
List<Customer> findByNameAndAge2(String name, int age);
```

The first parameter passed to the method is mapped to ?1, the second is
mapped to ?2, etc. If you accidentally switch these up - your query will likely
throw an exception, or silently produce wrong results.

### JPQL Query with Named Parameters
Named parameters can be referenced by name, no matter their position:

```
@Query("select c " +
        "from Customer c " +
        "where c.name = :name and c.age > :age")
List<Customer> findByNameAndAge2(@Param("name") String name, @Param("age") int age);
```

The name within the @Param annotation is matched to the named parameters
in the @Query annotation, so you're free to call your variables however you'd
like - but for consistency's sake - it's advised to use the same name.