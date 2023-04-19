# Spring Data JPA Repository Methods

Here we will discover functions related to repository.

## Famous functions

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
* deleteById()
* deleteAll()
* delete()
* test
* 