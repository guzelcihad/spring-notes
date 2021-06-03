Add this to the properties file

```
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true
          lower-case-service-id: true
```
By default url looks upper case we changed it to the lower case also.

## Spring Cloud Gateway
Built on top of spring webflux.
* Simple, yet effective way to route to APIs.
* Provide cutting concerns, security, health check
* Features, match routes on any request attribute