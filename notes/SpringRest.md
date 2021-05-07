## Controller
Spring 4.0 introduced the @RestController annotation in order to simplify the creation of RESTful web services. It's a convenient annotation that combines @Controller and @ResponseBody, which eliminates the need to annotate every request handling method of the controller class with the @ResponseBody annotation.
<br>
We can annotate classic controllers with the @Controller annotation. This is simply a specialization of the @Component class, which allows us to auto-detect implementation classes through the classpath scanning.
<br>
We typically use @Controller in combination with a @RequestMapping annotation for request handling methods.
Let's see a quick example of the Spring MVC controller:

```
@Controller
@RequestMapping("books")
public class SimpleBookController {

    @GetMapping("/{id}", produces = "application/json")
    public @ResponseBody Book getBook(@PathVariable int id) {
        return findBookById(id);
    }

    private Book findBookById(int id) {
        // ...
    }
}
```
We annotated the request handling method with @ResponseBody. This annotation enables automatic serialization of the return object into the HttpResponse.
<br>
@RestController is a specialized version of the controller. It includes the @Controller and @ResponseBody annotations, and as a result, simplifies the controller implementation:

```
@RestController
@RequestMapping("books-rest")
public class SimpleBookRestController {
    
    @GetMapping("/{id}", produces = "application/json")
    public Book getBook(@PathVariable int id) {
        return findBookById(id);
    }

    private Book findBookById(int id) {
        // ...
    }
}
```

## RequestMapping
```
     @RequestMapping(method = RequestMethod.GET, path = "/hello-world")
     public String helloWorld() {
        return "Hello";
     }
```
same as
```
 @GetMapping("/hello")
    public String hi() {
         return "hi";
     }
```

## Return a bean from rest service
Bean needs to have getter and setter for serialization.

add 
```
logging.level.org.springframework = debug
```
to see the detailed log.

## FAQS
What is dispatcher servlet?
> When looking at the logs there is log like this
    ```
    DispatcherServletAutoConfiguration matched:
      - @ConditionalOnClass found required class 'org.springframework.web.servlet.DispatcherServlet' (OnClassCondition)
      - found 'session' scope (OnWebApplicationCondition)
    ```
    there is DispatcherServlet found in classpath. We added a starter web project. And this project has Spring MVC.
    So, spring boot auto configuration says that I found a servlet in classpath. So lets configure it.
    There are lots configuration happens in the background thanks to spring boot auto config.

Who is configuring dispatcher servlet?
> Spring boot autoconfiguration

What does dispatcher servlet do?

How does a bean object get converted to JSON?
> Jackson config found in classpath for this conversions.
There is a class called HttpMessageConverter in the classpath. It is responsible for mapping object to JSON.


Who is configuring the error mapping?
> Spring boot auto config. It creates a default error page.

> Dispatcher servlet is responsible for handling the request. when app initialized it maps root context /.
It is a front controller. It knows all the url's and controller for responsible to handle those urls.
So, it navigates the request to the related controller. Finds the related method to execute. Then it 
decide how to return a message from this method. To figure out this it looks for ResponseBody annotation.
This annotation basically message converter. Jackson is responsible for that conversion.

## PathVariable
```
@GetMapping("/bean/{name}")
public HelloWordBean get(@PathVariable String name) 
```

## RequestBody
Maps http message into object
```
@PostMapping("/users")
public void createUser(@RequestBody User user) 
```

## Return HTTP Methods in Response
```
    @PostMapping("/users")
    public ResponseEntity<Object> createUser(@RequestBody User user) {
        User save = userDaoService.save(user);

        URI location = ServletUriComponentsBuilder
                .fromCurrentRequest()
                .path("/{id}")
                .buildAndExpand(save.getId()).toUri();

        return ResponseEntity.created(location).build();
    }
```
## Error Handling
When an exception thrown from service, 500 internal server error occured. Yet
it doesn't make sense. We need to return meaningful status code.
Just add this annotation and it turns out a 404 error.
```
@ResponseStatus(HttpStatus.NOT_FOUND)
public class UserNotFoundException extends RuntimeException{

    public UserNotFoundException(String message) {
        super(message);
    }
}

```

## Validation
@Valid in method signature and add hibernate validation on the entity.
Like Size, NotNull
```
 @PostMapping("/users")
    public ResponseEntity<Object> createUser(@Valid @RequestBody User user) {
```
When the provided fields are not met then 400 Bad Request thrown.

## HATEOAS
A HATEOAS request allows you to not only send the data but also specify the related actions:
Hypermedia is an important aspect of REST. It lets you build services that decouple client and server to a large extent and let them evolve independently. The representations returned for REST resources contain not only data but also links to related resources. Thus, the design of the representations is crucial to the design of the overall service.

Spring HATEOAS: a library of APIs that you can use to create links that point to Spring MVC controllers, build up resource representations, and control how they are rendered into supported hypermedia formats (such as HAL).

### Why Do We Need HATEOAS?
The single most important reason for HATEOAS is loose coupling. If a consumer of a REST service needs to hard-code all the resource URLs, then it is tightly coupled with your service implementation. Instead, if you return the URLs, it could use for the actions, then it is loosely coupled. There is no tight dependency on the URI structure, as it is specified and used from the response.

We just changed the code like this
```
     @GetMapping("/users/{id}")
    public EntityModel<User> getUserById(@PathVariable int id) {
        User user = userDaoService.getById(id);

        if(user==null)
            throw new UserNotFoundException("id-"+ id);


        //"all-users", SERVER_PATH + "/users"
        //retrieveAllUsers
        EntityModel<User> resource = EntityModel.of(user);

        WebMvcLinkBuilder linkTo =
                linkTo(methodOn(this.getClass()).getAll());

        resource.add(linkTo.withRel("all-users"));

        //HATEOAS

        return resource;
    }
```

IT should return a response like this
```
{
    "id": 1,
    "name": "John",
    "birthDate": "2021-05-06T15:47:02.358+00:00",
    "_links": {
        "all-users": {
            "href": "http://localhost:8080/users"
        }
    }
}
```

# Advanced Topics
## Internationalization
Customizing services for different kind of people around the world.
To achieve that we need to use 
![alt text](/images/spring-rest/1.PNG)