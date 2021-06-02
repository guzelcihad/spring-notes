There is a starter project in order to apply validation stuffs.
It consists hibernate validator.

```
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-validation</artifactId>
		</dependency>
```

`@Valid` add to the method parameters and jpa annotations to the bean.<br>
We can also define custom exception handling for validations.
```
    @Override
    protected ResponseEntity<Object> handleMethodArgumentNotValid
            (MethodArgumentNotValidException ex, HttpHeaders headers, HttpStatus status, WebRequest request) {

        ExceptionResponse exceptionResponse = new ExceptionResponse().setTimestamp(new Date())
                .setMessage("Validation failed")
                .setDetails(ex.getBindingResult().toString());

        return new ResponseEntity(exceptionResponse, HttpStatus.BAD_REQUEST);
    }
```