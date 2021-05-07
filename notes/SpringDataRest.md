An umbrella project of Spring Data.
<br>
We can think this project Spring Data repositories exposed as RESTful web services.
Spring data repositories can be exposed as hypermedia driven RESTful web services.
<br>
What is hypermedia?

> HATEOAS -> Hypermedia As the Engine of Application State
A rest api should o more than expose resource endpoints over HTTP,
* It should also expose API Documentation
* Automatically provide navigation between resources
Hypermedia driven API's accomplist just that.

![alt text](/images/spring-data-rest/1.PNG)

In Spring data rest we don't need RestController annotation.
<br>
How it works?

* Finds all of the spring data repositories
* Creates and endpoint that matches the entity name
* Appends an S to the Entity Name in the Endpoint
* Exposes operations as Restful APIs over HTTP 

## Configuring the Rest url path
Add annotation at class level

```
@RepositoryRestResource(path="bug")
public interface TicketRepository extends JpaRepository<Ticket, Integer> {}
```
This is gonna expose a collection of resource at the endpoint
```
http://localhost:8080/bug/
```

Configuring query method
Query method resources exposed under resource search
```
http://localhost:8080/bug/search/findByAppId
```
```
@RepositoryRestResource(path="bug")
public interface TicketRepository extends JpaRepository<Ticket, Integer> {
    List<Ticket> findByAppId(Integer id);
}
```
To customize path in method add 
```
@RestResource(path = "apps")
List<Ticket> findByAppId(Integer id);

```
endpoint will be:
```
http://localhost:8080/bug/search/apps
```
If you want to hide certain repositories, query methods or fields
```
// use at the class level
@RepositoryRestResource(exported = false)
```
or 
```
// use at the method levet
@RestResource(exported = false)
```

If you want to hide the crud methods, overwrite them like this:
```
@Override
@RestResource(exported = false)
void delete(Long id);

@Override
@RestResource(exported = false)
void delete(Long id);
```