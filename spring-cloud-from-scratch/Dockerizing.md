We already have this plugin just add some extra info.    

```     
    <build>
        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                    <configuration>
                        <image>
                            <name>guzelcihad/spring-cloud-${project.artifactId}:${project.version}/</name>
                        </image>
                        <pullPolicy>IF_NOT_PRESENT</pullPolicy>
                    </configuration>
                </plugin>
            </plugins>
        </pluginManagement>
    </build>
``` 

Then run the command to build image:
``` 
mvn spring-boot:build-image
``` 

> PS: Maven plugin takes so much time so I decided to use jib plugin from google.

Just add dependency:
``` 
 <plugin>
    <groupId>com.google.cloud.tools</groupId>
    <artifactId>jib-maven-plugin</artifactId>
    <version>2.5.2</version>
</plugin>
``` 
Then run the command:
``` 
mvn compile jib:build -Dimage=<docker registry name>/usersignup:v1
``` 

## Connecting App to Eureka
When we start containers, ms can not connect to eureka container.<br>
In this step, we provide an environment varible to currency-exchange service section.<br>
This variable actually defined in the exchange microservices properties file like this:
``` 
eureka:
  client:
    service-url:
      default-zone: http://localhost:8761/eureka
``` 
We just changed the localhost section and define it like this:
```
eureka:
  client:
    service-url:
      default-zone: http://naming-server:8761/eureka 
``` 

> PS: naming-server is the services defined in the docker compose file. 
