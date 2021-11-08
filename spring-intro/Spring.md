# Quick Intro

Spring is a dependency injection framework. What is dependency?

Lets take a look at an example

```
public class ComplexBusinessService {
    SortAlgorithm s;
}
```

Sort algorithm is a dependency of business logic class.<br>
Before Spring this is how we used to create dependencies.

```
public class ComplexBusinessService {
    SortAlgorithm s = new BubbleSortAlgorithm();
}

public class BubbleSortAlgorithm implements SortAlgorithm
```

What if you want to use a different sort algorithm? <br>
This kind of code is complex because service directly instantiating dependency.
This is called tight coupling.<br>

Remove tight coupling.

```
public class ComplexBusinessService {
    SortAlgorithm s;

    public ComplexBusinessService(SortAlgorithm s) {
        this.s = s;
    }

}

public class BubbleSortAlgorithm implements SortAlgorithm

SortAlgorithm s = new BubbleSortAlgorithm();
ComplexBusinessService bService = new ComplexBusinessService(s);
```

Now we create loosely coupled dependency.<br>
We just creating objects and populates dependencies. This is where Spring Framework comes in.
It instantiates objects and populates dependencies. It is our job to tell Spring what object are dependency and which will be created?<br>
We use annotations to tell Spring.

```
@Component
public class ComplexBusinessService {

    @Autowired
    SortAlgorithm s;
}

@Component
public class BubbleSortAlgorithm implements SortAlgorithm
```

We put @Component annotation to the business class to tell Spring you need to start managing instances of the complex business service class.
<br>
To define dependencies we put @Autowired

<br>
Core logic of the Spring framework is dependency injection.

### Terminology
* Beans -> ComplexBusinessService and BubbleSortAlgorithm, these instances that Spring manages are called beans. Beans are objects that 
Spring manages.

* Auworiring -> Spring identifies the dependencies and populates them. That process are called autowiring.
* Dependency Injection -> injecting sort algorithm as a dependency into the complex business object. 
* Inversion of Control -> We give the dependency injection control to the framework. This is called IOC.
* IOC Container -> Generic terminology that represents anything implements IOC. In Spring, typical IOC container is the Application Context. Also Bean Factory.
* Application Context -> is the one where all the beans are created and managed.

# Intro to Spring Framewok
These are the steps to Spring does

* What are the beans? 
* What are the dependencies of a bean?
* Where to search for beans

Once Spring scannes for components, then creating instance of bean

### Contructor vs Setter Injection
There are some dependencies which are optional and there are some dependencies which are mandatory.
If you have a dependency and the class can work without dependency then this is called optional dependency.
<br>
If you have optional dependency, use setter injection, otherwise use constructor injection.
<br>
If you don't explicitly define a setter method and use @Autowired on the field, this is also called setter injection.

## Autowiring in depth
What happens if a dependency sortAlgorithm available and there are multiple beans also available.
How Spring decides which sort algorithm bean to use?

### Primary Annotation
If you have more than one bean as dependency, Spring fails. You need to specify one bean as primary. 
<br>
So we use @Primary annotation for bean.

### By name
The other alternative is autowire by name. 
If we rename like this For ex: SortAlgorithm bubbleSortAlgorithm, it look for bean named BubbleSortAlgorithm

### Qualifier
Just add this to the bean definition, then specify it also where dependency will be injected.

## Bean Scopes
* Singleton -> one instance per Spring Context
* Prototype -> new bean whenever requested
* Request   -> one bean per http request
* Session   -> one bean per http session

Default is singleton.
We use 
```
@Scope(ConfigurableBeanFactory.SCOPE_SINGLETON)
```
to specify it.

PS: In Spring singleton, the behaviour is one instance per app. context. Homever there can be multiple context per JVM.
In GOF singleton, the behaviour is one instance per JVM.

## Component Scan
@ComponentScan annotation used for looking for beans in the specified packages. @SpringBootApplication defined class looks for 
its root and its sub packages. If we are trying to access a bean that is not defined in these packages then we need to define
package to scan.

## Bean Lifecycle
When a bean created and its dependencies populated then a method have @PostContruct annotation are called.
<br>
When a bean instance is removed from container then a method have @PreDestroy annotation are called.

## CDI
It defines an API for dependency injection.
JAVA EE dependency injection standards.
Spring supports most of annotations of it.
* @Inject (@Autowired)
* @Named (@Component, @Qualifier)
* Singleton (Defines a scope of singleton)

## Removing Spring Boot Dependency
If we remove Spring Boot configs to Bootstrap the app, then we need to create app. context
with the annotation like @Configuration and @ComponentScan which Spring Boot does for us.

## IOC vs Application Context vs Bean Factory
We give the responsibility of the dependency management to the Spring. This is called IOC. 
<br>
And the program or framework which provides us called IOC container. 
<br>
In Spring there are two implementations of the IOC Container. Application Context and Bean Factory.
<br>
Spring recommends us to use Application Context. 
<br>
We can think of Application Context as the extended version of Bean Factory.
<br>
It has all features of Bean Factory has and plus some features like AOP, web app context etc.
<br>
Core Spring functionality are provided in Spring-core.jar. And thats what the Bean Factory provides us.
It provides basic management of beans and wiring dependencies.

Summarize:
* IOC is a concept - Inversion of Control. Instead of the programmer injecting dependencies, the framework takes the responsibility of auto wiring.

* ApplicationContext is the Spring implementation of IOC.

* Bean Factory is the basic version of IOC Container.

* Application Context adds in all the features that are typically needed by enterprise applications.

## Component Annotations
These are the annotations that acts as Component provides us. But spesific to the layers.
* Repository -> encapsulating relation with database.
* Service -> Business Service Facade
* Controller -> Controller in MVC pattern.

This annotations allows you to classify your components into different categories and you can apply different
logic for each of these categories. For ex; Spring provides a default exception translation facility if you use
@Repository. There are a lot of JDBC exceptions and Spring classifies them and translates them. And that
feature only provided if you use @Repository annotation.

Also, lets say we want to log everything coming into business layer. In that scenario we'll be identify 
everything that has an @Service annotation

# Unit Testing with JUnit
* @Before runs before every tests.
* @After runs after every tests.
* @BeforeClass runs before all tests.
* @AfterClass runs after all tests.

There are some naming changes in JUnit5

JUnit4 | JUnit5
--- | --- |
@Before | @BeforEach | 283 |
@After | @AfterEach | 283 |
@BeforeClass | @BeforeAll | 283 |
@AfterClass | @AfterAll | 283 |

These are some changes.

# Spring Boot
We have lots of microservices and we want to build them quickly. Spring Boot helps us to achieve that.
<br>
Goals are:
* Enable building productions ready applications quickly.
* Provide common non-functional features like embedded servers, metrics, health checks, externalized configs.

What Spring Boot is NOT!
* Neither an app server nor a web server

Features
* Quick starter projects with auto configuration. For ex: we need a web project, Spring Boot creates all dependency for us
like Spring mvc, Spring core, validation and logging framework and to configure all this stuffs.

* Embedded Servers. We don't need to create war and deploy it to an app or web server.
* Production ready features like metrics and health checks. For ex; we can find out how many times a particular service is called.

## What is Spring Boot Auto Configuration
There are 3 things that are done by Spring Boot.
* It indicates that this is a Spring context
* Enables Auto Configuration
* Enables automatic component scan

Auto configuration is the most important stuff. Spring Boot loops through the classpath.
For ex when it sees Spring MVC in the classpath, it decides to configure dispatcher servlet.

## Spring Actuator
Brings monitoring capabilities to Spring app.
<br>
Exposes a lot of rest services to monitor app.

## Spring Developer Tools
When we change something in code, it needs to publish again to see the effect.
But this is a little bit time consuming thing.
This is where Spring developer tools come to the rescue.
<br>
It is a maven dependency. When we add it, make changes, it automatically restarts the server
but it takes less time. Because it doesn't load all maven dependency etc.

# SPRING AOP
When you build an app you typically have business, data, presentation layer.
But there are other stuff that doesn't have a layer but need to consider like logging, security.
We call these cross-cutting concerns. And the AOP is the best approach to implementing these concerns.

When we want to intercept a business method call, and do some stuff before it called, we use AOP.
For ex: you might want to check a user access spesific method before call.
We define a security in one place and apply to all business methods. This is what AOP provides us.

## Terminology
Lets look at this example.

```
//AOP
//Configuration
@Aspect
@Configuration
public class UserAccessAspect {
	
	private Logger logger = LoggerFactory.getLogger(this.getClass());
	
	//What kind of method calls I would intercept
	//execution(* PACKAGE.*.*(..))
	//Weaving & Weaver
	@Before("com.in28minutes.Spring.aop.Springaop.aspect.CommonJoinPointConfig.dataLayerExecution()")
	public void before(JoinPoint joinPoint){
		//Advice
		logger.info(" Check for user access ");
		logger.info(" Allowed execution for {}", joinPoint);
	}
}
```

* Pointcut is the expression we used in method header with @Before annotation which defined what kinds of method we would 
want to intercept.

* Advice is the method body, which states for what should we do when we intercept.
* The combination of pointcut and advide is called Aspect.
* The last term is Join Point. Its a spesific interception of a method call.
* Weaving is the process of the implementing the AOP around your method calls. The framework which implements is called weaver.

The another annotations are:
* @AfterReturning
* @After
* @AfterThrowing

@Around is usefull for advice performance. When we use this annotation, it intercepts the method then allow to execute
it. It captures the event the start and the finish time.

# Spring JDBC
Spring Boot auto configures beans under the hood. Spring JDBC provides us more readable and maintanable code 
comparing to JDBC.
