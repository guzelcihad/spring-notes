Provides production ready features for monitoring.

Add this dependency 

```
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>
```

Open this url `http://localhost:8080/actuator`.<br>
Add this `management.endpoints.web.exposure.include=*` to the application.properties
to enable everything.