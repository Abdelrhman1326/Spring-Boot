Here's why creating custom exceptions like `ResourceNotFoundException` is a standard best practice in Spring Boot:

### 1. Translates Code Logic into Proper HTTP Status Codes
By default, if your code throws a standard Java exception like `RuntimeException` or `NullPointerException`, Spring Boot doesn't know what went wrong—it just assumes something crashed. As a result, it returns a generic **`500 Internal Server Error`**.

A `500` status tells the frontend/client: _"Our server code broke."_

By creating `ResourceNotFoundException` and adding `@ResponseStatus(HttpStatus.NOT_FOUND)`, you tell Spring: _"When this specific exception happens, it's not a server crash—the client asked for something that doesn't exist."_ Spring automatically translates this into a **`404 Not Found`** response.

### 2. Keeps Your Service Code Clean & Readable
Without custom exceptions, handling missing data can make your service logic messy.

**Without custom exceptions:**
``` Java
// Cluttered with HTTP response building inside the business logic
public ResponseEntity<LinkResponse> updateLink(Long id, User user, LinkUpdateRequest request) {
    Optional<Link> linkOpt = linkRepository.findByIdAndPageUserId(id, user.getId());
    if (linkOpt.isEmpty()) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).build(); 
    }
    Link link = linkOpt.get();
    // ... rest of update code
}
```

**With custom exceptions:**
``` Java
// Clean, expressive, functional style
public LinkResponse updateLink(Long id, User user, LinkUpdateRequest request) {
    Link link = linkRepository.findByIdAndPageUserId(id, user.getId())
            .orElseThrow(() -> new ResourceNotFoundException("Link not found with id: " + id));

    // ... rest of update code
}
```

### 3. Provides Specific, Meaningful Context
If you throw a generic exception, logging and debugging become difficult. When you see `ResourceNotFoundException` in your server logs or application monitoring tool (like Datadog or Sentry), you immediately know what failed without having to inspect stack traces line-by-line.

### 4. Enables Centralized Error Handling
As your application grows, you can pair custom exceptions with `@RestControllerAdvice`. This allows you to construct consistent JSON error responses across your entire API instead of returning default Spring error pages.

For example, whenever _any_ part of your application throws a `ResourceNotFoundException`, you can automatically format the JSON response like this:

``` Java
{
  "timestamp": "2026-07-30T17:08:25",
  "status": 404,
  "error": "Not Found",
  "message": "Link not found with id: 42"
}
```

### Example of a Custom Exception

``` Java
package com.backend.linkshare.exception;  
  
import org.springframework.http.HttpStatus;  
import org.springframework.web.bind.annotation.ResponseStatus;  
  
@ResponseStatus(HttpStatus.NOT_FOUND)  
public class ResourceNotFoundException extends RuntimeException {  
    public ResourceNotFoundException(String message) {  
        super(message);  
    }  
}
```

`@ResponseStatus(HttpStatus.NOT_FOUND)` tells spring to return a status code **404** (which it widely well known to mean **not found**) when using the custom exception **ResourceNotFoundException** anywhere.

The **ResourceNotFoundException** takes a **message** as a **String** argument and then pass it to the parent constructor (the constructor of **RuntimeException**) which make it easy to throw the custom exception anywhere with a passed custom message according to the context need.


### When to Use **Unchecked Exceptions** (`extends RuntimeException`)
Use unchecked exceptions for **unrecoverable errors, programming bugs, or bad client requests** where the caller cannot reasonably fix the issue programmatically on the spot.

- **Missing / Unauthorized Resources:** Requested record doesn't exist (`ResourceNotFoundException`), or the user lacks permission (`AccessDeniedException`).
- **Invalid Arguments / Bad Payloads:** Null pointers, array index out of bounds, or illegal parameters passed to a method.
- **Infrastructure Failures:** Database connectivity loss, network timeouts, or hardware issues.

> **Rule of Thumb for Web APIs:** **Use unchecked exceptions almost everywhere.** They allow errors to bubble up naturally to global error handlers (`@RestControllerAdvice`) without cluttering your business logic with signature noise (`throws`).

### When to Use **Checked Exceptions** (`extends Exception`)
Use checked exceptions for **foreseeable, recoverable business conditions** where you explicitly want to force the caller to handle the situation immediately at compile time.

- **Recoverable Fallbacks:** Payment gateway fails with a soft decline $\rightarrow$ caller is forced to attempt a fallback payment method or prompt the user for retry logic.
- **Expected External Failures:** Reading a configuration file from disk that might be missing $\rightarrow$ caller is forced to handle creating a default configuration file.
- **Strict API Contracts:** Designing a core core library/SDK where you want to mandate that downstream developers handle a specific scenario explicitly.
ddd