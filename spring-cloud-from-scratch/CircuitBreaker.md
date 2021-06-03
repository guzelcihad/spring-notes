We use resilience4j. It inspired by netflix hystrix.<br>
Add these dependencies. AOP and resilience4j
```
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-aop</artifactId>
		</dependency>

		<dependency>
			<groupId>io.github.resilience4j</groupId>
			<artifactId>resilience4j-spring-boot2</artifactId>
		</dependency>
```

We can add `@Retry("sample-api")` annotation 
to the controller method. When a request made to that method, it tries 3 times
before returning error<br>
We can also override this retry attemp
```
resilience4j.retry.instances.sample-api.max=5
```
<p>

We can add fallback method for errors.
```
	//@Retry(name = "sample-api", fallbackMethod = "hardcodedResponse")

    public String hardcodedResponse(Exception ex) {
		return "fallback-response";
	}
```
## Enable Circuit Breaker
When we add 
```
	//@CircuitBreaker(name = "default", fallbackMethod = "hardcodedResponse")
```   
we implement the circuit breaker actually. We add this annotation to the controller.

## Rate Limiting
We can limit the request made of our controller. For ex: allow 1000 request per second.
Add this annotation and configurations:

```  
@RateLimiter(name="default")
resilience4j.ratelimiter.instances.default.limitForPeriod=2         # allow two request
resilience4j.ratelimiter.instances.default.limitRefreshPeriod=10s   # in every ten seconds
```  

## Concurrent Calls
How many concurrent calls allowed. Its called bulkhead.

```  
@Bulkhead(name="sample-api")
resilience4j.bulkhead.instances.default.maxConcurrentCalls=10
resilience4j.bulkhead.instances.sample-api.maxConcurrentCalls=10
```  