
# Nullable Objects vs. `HttpStatus.NOT_FOUND` in Spring Boot

When implementing REST API endpoints, we often need to retrieve entities from the database using a Spring Data repository. Since the requested entity may not exist, we must handle this case before performing any operations on the returned object.

If a missing entity is not handled properly, attempting to call a method on it (for example, `link.setSortOrder(...)`) would result in a `NullPointerException`. Spring Boot would then return a **500 Internal Server Error**, indicating that the server encountered an unexpected error. However, this is misleading because the server is functioning correctly—the requested resource simply does not exist.

Instead, the API should detect this situation and return an appropriate HTTP status code, such as **404 Not Found**, allowing the frontend to understand what actually happened.

## Ignoring Missing Objects

In some scenarios, ignoring missing entities is acceptable.

For example, suppose an endpoint processes a collection of objects. Some objects may exist, while others may not. If the endpoint should continue processing the valid objects and ignore the missing ones, we can simply skip the missing entities instead of throwing an exception.

```
@Transactional
public void updateSortOrder(User user, Map<Long, Integer> idOrder) {
    for (Map.Entry<Long, Integer> entry : idOrder.entrySet()) {
        Optional<Link> link = linkRepository.findByIdAndPageUserId(
                entry.getKey(),
                user.getId()
        );

        link.ifPresent(value -> value.setSortOrder(entry.getValue()));
    }
}
```

### Understanding `Optional`

Spring Data JPA commonly returns an `Optional<T>` when an entity may not exist.

An `Optional<Link>` does **not** contain a `null` object. Instead, it represents one of two possible states:

- It contains a `Link` (`Optional.of(link)`).
    
- It is empty (`Optional.empty()`).
    

Using `Optional` forces us to explicitly handle the case where no entity is found, instead of accidentally working with a `null` reference.

Common ways to handle an `Optional` include:

- `ifPresent(...)` — Execute code only if a value exists.
    
- `orElse(...)` — Return a default value if none exists.
    
- `orElseThrow(...)` — Throw an exception if the value is absent.
    

Using `Optional.get()` directly is generally discouraged because it throws a `NoSuchElementException` if the `Optional` is empty.

## Returning `404 Not Found`

In many endpoints, ignoring a missing entity is **not** appropriate.

If the client requests an entity that does not exist (or does not belong to the authenticated user), the endpoint should stop processing and return an appropriate HTTP response.

A common way to achieve this is by using `orElseThrow()` together with `ResponseStatusException`.

```
@Transactional
public void updateSortOrder(User user, Map<Long, Integer> idOrder) {
    for (Map.Entry<Long, Integer> entry : idOrder.entrySet()) {
        Link link = linkRepository.findByIdAndPageUserId(
                entry.getKey(),
                user.getId()
        ).orElseThrow(() -> new ResponseStatusException(
                HttpStatus.NOT_FOUND,
                "Link with id " + entry.getKey() + " not found"
        ));

        link.setSortOrder(entry.getValue());
    }
}
```

`ResponseStatusException` tells Spring Boot to immediately stop processing the request and return the specified HTTP status code.

In this case, the client receives a **404 Not Found** response instead of a generic **500 Internal Server Error**.

Example response:

```
{
  "timestamp": "2026-08-01T18:28:50.123+00:00",
  "status": 404,
  "error": "Not Found",
  "message": "Link with id 5 not found",
  "path": "/api/links/reorder"
}
```

## Summary

- Always handle the possibility that a requested entity does not exist.
    
- Use `Optional` to explicitly represent the possible absence of a value.
    
- Use `ifPresent()` when missing entities can safely be ignored.
    
- Use `orElseThrow()` when the request cannot continue without the entity.
    
- Throwing `ResponseStatusException(HttpStatus.NOT_FOUND, ...)` allows the API to return a meaningful **404 Not Found** response instead of an incorrect **500 Internal Server Error**.