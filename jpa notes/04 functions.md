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
1. Method name should start with one of the following prefixes:
   * find...by
   * read...by
   * query...by
   * count...by
   * get...by



