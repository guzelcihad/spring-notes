## Configuring H2 in project
Just add
```
spring.h2.console.enabled=true
```
to application.properties.

Service is gonna run at the /h2-console URI.
<br>
With the latest versions of Spring Boot (2.3+), the H2 database name is randomly generated each time you restart the server.

You can find the database name and URL from the console log.
Add this to application properties
```
spring.datasource.url=jdbc:h2:mem:testdb
spring.data.jpa.repositories.bootstrap-mode=default
```

## Initialize the H2 DB
add a data.sql file to src/main/resources for Spring Boot release greater than 2.5.0
if you use this version just add this properties to application.properties.
```
spring.jpa.defer-datasource-initialization=true
```
or use schema.sql instead of data.sql
<br>
When db first initialized it is gonna execute command in this file.

## JDBC vs Spring JDBC
Jdbc is very complex and unmaintaineble code. Spring saves from those boilerplate codes.

```
@Entity // mark class as entity
```
```
@Column //mark fielda as column
```
```
@Id
@GeneratedValue //let hibernate managed the id, typically it creates a sequence in db
```
* We need to have a no args contructor
```
// connect to the database
@PersistenceContext
EntityManager entityManager;
```

```
spring.jpa.show-sql=true
```

## Some configs that helps
```
# Enabling H2 Console
spring.h2.console.enabled=true

#Turn Statistics on
spring.jpa.properties.hibernate.generate_statistics=true
logging.level.org.hibernate.stat=debug

# Show all queries
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
logging.level.org.hibernate.type=trace
```
When this configs defined the app. logs becomes like this.
![alt text](/images/jpa/1.PNG)

## Cache
Two level cache available. First level cache lives within a single transaction.
Second level cache is cache across all transactions.

Here is how first level cache works.
```
public void findById() {
    Course course = repository.findById(100l);
    Course course1 = repository.findById(100l);
} 
```

If we call this method twice then jpa will go to db twice. But if we put @Transactional annotation to the method
then the second method call doesn't go to db. Because it will be cached. Because first level cache lives within the 
boundaries of the transaction.
<br>
In the first example it doesnt cached because the two method calls live within another transaction boundaries.
<br>
Q1: What is the difference to use Transactional annotation at class level and method level?

> In case 1 @Transactional is applied to every public individual method. Private and Protected methods are Ignored by Spring. In case 2 @Transactional is only applied to method2(), not on method1()

> Case 1: - Invoking method1() -> a transaction is started. When method1() calls method2() no new transaction is started, because there is already one

> Case 2: - Invoking method1() -> no transaction is started. When method1() calls method2() NO new transaction is started. This is because @Transactional does not work when calling a method from within the same class. It would work if you would call method2() from another class.
Yukarıdaki ilk örnekte repository class üzerinde transaction anotasyon vardı. Bu sebeple o sınıfın herhangi
bir methodu çağrıldığında hepsi aynı transaction içinde yaşar.

### Second Level Cache
Second level cache needs configuration. We can use ehcache.
@Cacheable anotasyonu entity class'a ekleyerek o entity'i cacheleyebiliriz.

## Hard Delete Soft Delete
We just add an entity called isDeleted and add an annotation to the top of the entity @SQLDelete.
Its a hibernate annotations not JPA.

```
@SQLDelete(sql="update course set is_deleted=true where id=?")
```
Filtering Entities with @Where

> Suppose we want to provide an additional condition to the query whenever we request some entity.

> For instance, we need to implement “soft delete”. This means that the entity is never deleted from the database, but only marked as deleted with a boolean field.

> We'd have to take great care with all existing and future queries in the application. We'd have to provide this additional condition to every query. Fortunately, Hibernate provides a way to do this in one place:

To be able to fetch the true records in select statements we just need to add annotation
```
@Where(clause="is_deleted = false")
```
This annotation adds this criteria in every select statements.
<br>
This annotation doesnt apply to native queries.