Create project, create git repo that store configurations and add this config in 
configuration project properties file.

```
spring.application.name=spring-cloud-config-server
server.port=8888
spring.cloud.config.server.git.uri=file:///in28Minutes/git/spring-microservices-v2/03.microservices/git-localconfig-repo
#file:///C:/Users/home/Desktop/yourProject/git-repo
```

This repository consist of separate configuration files for different projects.<br>
for ex: `limits.properties`, `currency-exchange.properties`

Now
* Add `EnableConfigServer` to the main class
* Open the url and hit `localhost:8888/limits-service/default`

`limits-service` is the name of the properties file stored in git repo.<p>
We add this property to the microservices who wants fetch the config:
```
spring.config.import=optional:configserver:http://localhost:8888
```
As you may guess, 8888 is the url where config server hosted.