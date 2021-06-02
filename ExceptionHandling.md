These are the three annotation that helps us.
* @ResponseStatus
* @ExceptionHandler
* @ControllerAdvice

## Default Exception Handling
If we call the /product API with an invalid id the service will throw a NoSuchElementFoundException runtime exception and we’ll get the following response:
```
{
  "timestamp": "2020-11-28T13:24:02.239+00:00",
  "status": 500,
  "error": "Internal Server Error",
  "message": "",
  "path": "/product/1"
}
```

We can see that besides a well-formed error response, the payload is not giving us any useful information. Even the message field is empty, which we might want to contain something like “Item with id 1 not found”.

<br>

Spring Boot provides some properties with which we can add the exception message, exception class, or even a stack trace as part of the response payload:

```
server:
  error:
  include-message: always
  include-binding-errors: always
  include-stacktrace: on_trace_param
  include-exception: false
```

Now if we call the /product API again with an invalid id we’ll get the following response:

```
{
  "timestamp": "2020-11-29T09:42:12.287+00:00",
  "status": 500,
  "error": "Internal Server Error",
  "message": "Item with id 1 not found",
  "path": "/product/1"
} 
```

Moving on! The status and error message - 500 - indicates that something is wrong with our server code but actually it’s a client error because the client provided an invalid id.

## ResponseStatus
As the name suggests, @ResponseStatus allows us to modify the HTTP status of our response. It can be applied in the following places:

```
@ResponseStatus(value = HttpStatus.NOT_FOUND)
public class NoSuchElementFoundException extends RuntimeException {
  ...
}

{
  "timestamp": "2020-11-29T09:42:12.287+00:00",
  "status": 404,
  "error": "Not Found",
  "message": "Item with id 1 not found",
  "path": "/product/1"
} 
```

## ExceptionHandler
The @ExceptionHandler annotation gives us a lot of flexibility in terms of handling exceptions. For starters, to use it, we simply need to create a method either in the controller itself or in a @ControllerAdvice class and annotate it with @ExceptionHandler:

```
 @ExceptionHandler(NoSuchElementFoundException.class)
  @ResponseStatus(HttpStatus.NOT_FOUND)
  public ResponseEntity<String> handleNoSuchElementFoundException(
      NoSuchElementFoundException exception
  ) {
    return ResponseEntity
        .status(HttpStatus.NOT_FOUND)
        .body(exception.getMessage());
  }
```

## ControllerAdvice
Controller advice classes allow us to apply exception handlers to more than one or all controllers in our application: