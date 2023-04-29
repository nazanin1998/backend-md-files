# Pagination

Pagination in a RDB is so easy.

### 1. Develop method in the repository
First you need to define the ___findAll___ method in the repository. Following code
presents how it can be done.

```
public interface CustomerRepository extends JpaRepository<Customer, Long> {

    Page<Customer> findAll(Pageable pageable);
}
```

### 2. Develop service layer method
In service layer method you need to develop a method, which builds a pageable and
passes it to the findAll method. Like:

```
public Page<Customer> findCustomersInPagination(int pageNumber, int pageSize, String sortBy, String sortDir) {

    Sort sort = sortDir.equalsIgnoreCase(Sort.Direction.ASC.name()) ? Sort.by(sortBy).ascending() : Sort.by(sortBy).descending();
    Pageable pageable = PageRequest.of(pageNumber, pageSize, sort);

    return customerRepository.findAll(pageable);

}
```

### 3. Develop the API
Finally, you need to develop the API in the controller class.

```
@GetMapping("/get-customers-in-pagination")
    public ResponseEntity<?> getCustomersInPagination(
            @RequestParam int pageNumber,
            @RequestParam int pageSize,
            @RequestParam  String sortBy,
            @RequestParam String sortDir
    ) {

        return ResponseEntity
                .ok(customerService.findCustomersInPagination(pageNumber, pageSize, sortBy, sortDir));
    }
```