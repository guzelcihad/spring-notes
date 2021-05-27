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
![alt text](../images/jpa/1.PNG)

## Writing Test Methods For Repository
The @SpringBootTest annotation tells Spring Boot to look for a main configuration class (one with @SpringBootApplication, for instance) and use that to start a Spring application context. 

```
A nice feature of the Spring Test support is that the application context is cached between tests. That way, if you have multiple methods in a test case or multiple test cases with the same configuration, they incur the cost of starting the application only once. You can control the cache by using the @DirtiesContext annotation.
```
You need to use @DirtiesContext where you test your data changes. Because you can delete, update or insert
data and other test should not affect these changes. So we use this annotation to reset the changes
happened in current unit test.

```
@RunWith(SpringRunner.class)
@SpringBootTest(classes=DemoApplication.class)
public class CourseRepositoryTest {
	
	private Logger logger = LoggerFactory.getLogger(this.getClass());
	
	@Autowired
	CourseRepository repository;
	
	@Test
	public void findById_basic() {
		Course course = repository.findById(10001L);
		assertEquals("JPA in 50 Steps", course.getName());
	}

}
```

## Delete By Id
When we call this method
```
    public void deleteById(Long id){
        Course course = findById(id);
        em.remove(course);
    }
```

Spring raises an error that it needs a transaction. So we add Spring @Transactional annotation
to the repository.


## EntityManager
```
public void playWithEntityManager() {
		Course course = new Course("Web Services in 100 Steps");
		em.persist(course);
		course.setName("Web Services in 100 Steps - Updated");
	}
```
When we run this code, it first creates an insert script then an update scripts. Why?
Because we use @Transactional at the class level and EntityManager keep tracks the
changes in method.

### Methods
em.flush() eagerly updates the changes in db.
em.detach(obj), breaks the keeping track the obj. So the changes
on that obj doesn't reflect on to database.
<br>
There is also another method em.clear() which breaks all the tracking objects.
<br>
em.refresh(obj1) => it refresh obj1 content from db. So if its value updated in transaction before
refresh method call, then its value gone. Because its replaced with the value exist in db.
 
```
public void playWithEntityManager() {
		
		Course course1 = new Course("Web Services in 100 Steps");
		em.persist(course1);	
		
		Course course2 = new Course("Angular Js in 100 Steps");
		em.persist(course2);

		em.flush();

		course1.setName("Web Services in 100 Steps - Updated");
		course2.setName("Angular Js in 100 Steps - Updated");
		
		em.refresh(course1);
		
		em.flush();
	}
```
## JPQL
Simple query returns the Query interface
```
Query query = em.createQuery("Select  c  From Course c");
		List resultList = query.getResultList();
```
Query that returns type via TypedQuery interface
```
TypedQuery<Course> query = 
					em.createQuery("Select  c  From Course c", Course.class);
		
		List<Course> resultList = query.getResultList();
```

## Annotations
* @Table(name="Course")
* @Column(name="name", nullable=false)
* @UpdateTimestamp => hibernate annotation not jpa, keeps track updated row
* @CreationTimestamp => same as above

## NamedQuery
We define query on top of entity:
```
@NamedQueries(value = { 
		@NamedQuery(name = "query_get_all_courses", 
				query = "Select  c  From Course c")})

TypedQuery<Course> query = em.createNamedQuery("query_get_all_courses", Course.class);
```

## NativeQuery
Just an sql query like select * , jpql queries are different it says select c from course
```
Query query = em.createNativeQuery("SELECT * FROM COURSE where id = ?", Course.class);
```

Queries with named parameters
```
Query query = em.createNativeQuery("SELECT * FROM COURSE where id = :id", Course.class);
		query.setParameter("id", 10001L);
```

# Entity RelationShips

## OneToOne
Student has passport in table.

If we define a method like this its gonna throw an error. Because passoword doesn't exist yet.
We first persist passport record.
```
Passport passport = new Passport("Z123456");
Student student = new Student("Mike");
student.setPassport(passport);
em.persist(student);
```
The correct way
```
Passport passport = new Passport("Z123456");
em.persist(passport);
        
Student student = new Student("Mike");
student.setPassport(passport);
em.persist(student);
```
* If we query for retrieve in Student entity, the query is eager by default. Meaning that
its gonna create a query with left join with passport table. Any OneToOne query is eager by default.

@OneToOne(fetch = FetchType.LAZY) is what we need.


```
	@Test
	@Transactional
	public void retrieveStudentAndPassportDetails() {
		Student student = em.find(Student.class, 20001L);
		logger.info("student -> {}", student);
		logger.info("passport -> {}",student.getPassport());
	}
```
If we are gonna run this method without Transactional, then it is gonna raise an exception in the
line student.getPassport(). Because hibernate thinks when we run find method
it kills the session.
<br>
In the student.getPassport() method another query fires up and it is gonna query the passport table lazily.

- Persistence Context it the place where the all entites are stored and managed. 
- We interact with the Persistence Context via EntityManager
* In hibernate session means persistence context, 

### Unidirectional Bidirectional Relations
In this student password example, if we put a field in password entity like this

```
@OneToOne(fetch=FetchType.LAZY)
	private Student student;
```
Then its gonna create studentId column in passport table.
<br>
This is not what we want because we already have this relation in student table. Student table
is the owner of this relation and this way we are gonna create duplication.
We just want one side holds this relation. That's why we add this code block in Pasport table.

<br>
@OneToOne(fetch=FetchType.LAZY, mappedBy="passport")
	private Student student;
<br>

We added this info, the non owner side. And we refer in mappedBy annotation to the
owner side prop.

## OneToMany
Course can have many reviews. A review can exist within the one course.
So, we need to add this to the Course class. 

```
@OneToMany(mappedBy = "course")
private List<Review> reviews = new ArrayList<>();
```

We can also define getters and setters. But we dont need to have setters.
Because it accepts for list of reviews. We usually don't need that.
Instead we need one review for course. So, in order to control that we can add this
methods.

```
    public void addReview(Review review) {
        reviews.add(review);
    }
    
    public void removeReview(Review review) {
        reviews.remove(review);
    }
```

And also we add this to the Review entity.
```
@ManyToOne
private Course course;
```
In this relation, review is the owner. Because we add courseid colomn to the review table.
So, mappedBy will be in course entity.

> PS: By default, the JPA @ManyToOne and @OneToOne annotations are fetched EAGERly, while the @OneToMany and @ManyToMany relationships are considered LAZY.

## ManyToMany

Course and Student entities are the example of this relation.
We just insert to list to the each class and annotated each other with ManyToMany.
<br>
But this is gonna create two tables in db called courses_student, student_courses.
We just expect it to create one table.
<br>
To be able to solve this problem we just gonna make one entity is the owning side of the
relationship.
<br>
For many to many relationship it is not important which entity is the owning side.
<br>
It is creating a table called STUDENT_COURSES with STUDENTS_ID, COURSES_ID.
<br>
We don't want this plurals in column names.  
We add this annotation in the owning side of the relation
```
    @ManyToMany
    @JoinTable(name = "STUDENT_COURSE",
        joinColumns = @JoinColumn(name = "STUDENT_ID"),
            inverseJoinColumns = @JoinColumn(name = "COURSE_ID"))
    private List<Course> courses = new ArrayList<>();
```
Why we did that?
Because when we don't define this relation names its gonna 

# INHERITANCE
4 types exist. Single table, table per class, joined, mapped super class.

So, just define entities:
```
@Entity
public abstract class Employee {

    @Id
    @GeneratedValue
    private Long id;

    @Column(nullable = false)
    private String name;
}
```

```
@Entity
public class FullTimeEmployee extends Employee {
    protected FullTimeEmployee() {
    }

    public FullTimeEmployee(String name, BigDecimal salary) {
        super(name);
        this.salary = salary;
    }

    private BigDecimal salary;

}
```
```
@Entity
public class PartTimeEmployee extends Employee {

    protected PartTimeEmployee() {
    }

    public PartTimeEmployee(String name, BigDecimal hourlyWage) {
        super(name);
        this.hourlyWage = hourlyWage;
    }

    private BigDecimal hourlyWage;

}
```

## Single Table
The default strategy if we dont specify it.

We just add this annotation the the Employee entity. And it just creates one table and holds
other parttime and full time emplyee like this format.
```
@Entity
@Inheritance(strategy=InheritanceType.SINGLE_TABLE)
public abstract class Employee {
```

| DTYPE   | ID  | NAME | HOURLY_WAGE | SALARY
|---|---|---|---|---|
|PartTimeEmployee|1|Jill|50.00|null|
|FullTimeEmployee|2|Jack|null|1000.00|

The drawbacks is so much null values may available. Data integrity is not really good.
The advantage, efficient because one table.

With this annotation we simply change dtype column to something more readable.
```
@DiscriminatorColumn(name="EmployeeType")
```

## Table Per Class
This strategy creates two table called FULL_TIME_EMPLOYEE and PART_TIME_EMPLOYEE respectively.
<br>
When we gett all employee informations hibernate creates a huge query, with union all.
```
@Inheritance(strategy=InheritanceType.TABLE_PER_CLASS)
```
The drawback is design of the table. If you have a lot of common columns they repeated 
in every tables.

## Joined
This strategy creates three table called EMPLOYEE, FULL_TIME_EMPLOYEE and PART_TIME_EMPLOYEE respectively.
<br>
subclasses table only have id column from super class.
```
@Inheritance(strategy=InheritanceType.JOINED)
```
This strategy holds common detail in base entity and spesific detail in sub entities.
Sub tables have foreign key id.

## Mapped Super Class
This strategy creates two tables called FULL_TIME_EMPLOYEE and PART_TIME_EMPLOYEE respectively.
<br>
This is a different kind of strategy. This strategy doesn't mark entity as entity.
This is just base for subentities.
```
@MappedSuperclass
//@Entity
//@Inheritance(strategy=InheritanceType.JOINED)
public abstract class Employee {
```

Q1: Difference between Table per Class vs Mapped Super Class
```
MappedSuperClass must be used to inherit properties, associations, and methods.

Entity inheritance must be used when you have an entity, and several sub-entities.

You can tell if you need one or the other by answering this questions: is there some other entity in the model which could have an association with the base class?

If yes, then the base class is in fact an entity, and you should use entity inheritance. If no, then the base class is in fact a class that contains attributes and methods that are common to several unrelated entities, and you should use a mapped superclass.

For example:

You can have several kinds of messages: SMS messages, email messages, or phone messages. And a person has a list of messages. You can also have a reminder linked to a message, regardless of the kind of message. In this case, Message is clearly an entity, and entity inheritance must be used.
All your domain objects could have a creation date, modification date and ID, and you could thus make them inherit from a base AbstractDomainObject class. But no entity will ever have an association to an AbstractDomainObject. It will always be an association to a more specific entity: Customer, Company, whatever. In this case, it makes sense to use a MappedSuperClass.
```

## Summarize
If you care data integrity, joined is perfect. Because it has foreign keys related with base entity.
If you care performance, single table should be pick.

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

# FAQS
## When Hibernate Sends Updates to Db?
Assume that we have this method. Hibernate keeps all the changes until the end of the method.
When method finished hibernate sends updates to db.

```
	@Transactional
    public void saveStudentWithPassword() {
        Passport passport = new Passport("Z123456");
        em.persist(passport);

        Student student = new Student("Mike");
        student.setPassport(passport);
        em.persist(student);

        student.setName("Cihad");
        passport.setNumber("34");
    } 
```

Lets say that we use em.flush() after the calling em.persist in student object.
And after that updates were failed. Even though this scenario hibernate rollbacks all the things.

## When we need @Transaction in Unit Test?
Assume that we add transaction annotation in the repository class.
<br>
Unit Test -> Repository -> EntityManager : no need to add 
Unit Test -> Entity Manager : need to add

## Do read only methods need a transaction?
If you have a couple of lines you read data, Yes we need to add.  
Because transaction is not only about data changes. For ex: when you read
something you may want to read latest values, not stale data.
And it is possible at the time when you are reading some thing the other
transaction may changes the value you are reading. Its about isolation.