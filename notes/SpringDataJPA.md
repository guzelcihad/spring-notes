# What is Spring Data JPA?
When we work with JPA, we build repository class to integrate with database and puts here
some boilerplate codes for crud operations. And also we need to build persistence.xml file
for setup configurations like entities.

Spring Data JPA saves us from this boilerplate codes.
It provides a high level API to simplify data tier.

## Spring Data Repositories
Difference from Jpa repositories;
* Java interfaces
* Mapi 1 to 1 with Entity
* Provides an implementation in runtime

## Repository Hierarchy

![](..\images\spring-data-jpa\1.PNG)

Only JpaRepository comes from Spring Data JPA, the other repositories are available in Spring Data that means we can use
in for example Spring Data MongoDb