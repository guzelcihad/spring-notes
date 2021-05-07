![alt text](/images/spring-boot/1.PNG)

## Before Spring
* Dependency versions compatibility was an big issue. For ex hibernate to spring.
* Configuration was really painful. We need to configure everyting. Thats a lot of work.
We configure, dispatcher servlet, web server, security and so on. Spring boot makes all
these kinds of issues for us. We just need to foucs on development and business logic.
* Starter projects are so much easy for us.


## What is Auto Configuration
@SpringBootApplication indicates that 
* this is a spring context
* auto configuration
* component scan

SPrintApplication.run => execute and create context , ApplicationContext
> Auto configurations looks for spesific class in classpath and configures it automatically

* @Configuration, Tags the class as a source of bean definitions for the application context.
* @EnableAutoConfiguration, Tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings. For example, if spring-webmvc is on the classpath, this annotation flags the application as a web application and activates key behaviors, such as setting up a DispatcherServlet
* @ComponentScan: Tells Spring to look for other components, configurations, and services in the com/example package, letting it find the controllers.

## What is CommandLineRunner
When the application context launches CommandLineRunner gets executed.
<br>
Application Runner and Command Line Runner interfaces lets you to execute the code after the Spring Boot application is started. You can use these interfaces to perform any actions immediately after the application has started. This chapter talks about them in detail.