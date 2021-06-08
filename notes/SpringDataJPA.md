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

## Query DSL
Method contracts
- Query dsl begins with findBy, queryBy, readBy, countBy, getBy
- Multiple criteria combined with "and", "or"


We use findBy prefix and after that we use entity names with camel case.
<br>

The equivalent of this query
```
    public List<Session> getSessionsThatHaveName(String name) {
        List<Session> ses = entityManager
                .createQuery("select s from Session s where s.sessionName like :name")
                .setParameter("name", "%" + name + "%").getResultList();
        return ses;
    }
```
is this:
```
List<Session> findBySessionNameContains(String name);
```
this method implemented in the background like this
```
//JPQL
select s from Session s
where s.sessionName like :name
```
and in sql
```
select * from session s where s.session_name like = ?
```

### Query Method Return Types

```
List<Session> findBySessionNameContains(String name);
Session findFistBySessionNameContains(String name);
Long countBySessionNameContains(String name);
```

the prefix decides the return types.

### And or
```
findByFirstNameAndLastName 
findByFirstNameOrLastName
```

### Equals-is-not
```
findBySessionLength
findBySessionLengthIs
findBySessionLengthEquals
findBySessionLengthNot

where a.sessionLength = ?1
where a.sesionLength = ?1
where a.sessionLength = ?1
where a.sessionLength != ?1
```

### Like not like
```
findBySessionNameLike("Java%")                  //We need to pass string in client like Java%
findBySessionNameNotLike("Python%")
```

### Like not like
Similar to like keyworde xcept the % is automatically added
```
findBySessionNameStartingWith("j") //j%
findBySessionNameEndingWith("j")   //%j
findBySessionNameContaining("j")   //%j
```


### Less than greater than
<, >, <=, >=
```
findBySessionLengthLessThan(30)
findBySessionLengthLessThanEqual(30)
```

### before after between
```
findByStartDateBefore(startDate)
findByStartDateBetween(startDate, endDate)
```

### null, is null, not null
```
findBySpeakerPhotoNull()
findBySpeakerPhotoIsNull()
```

### order by
```
findByLastNameOrderByFirstNameAsc(name)
```

## Query Annotation
Use for more complex queries.

```
@Query("query")
List<TicketPrice> get....();
```
We can use named parameters
```
@Query("query :maxprice")
List<TicketPrice> get....(@Param("maxPrice));
```
Native sql queries
```
@Query("query", nativeQuery=true)
List<TicketPrice> get....();
```
Modifiable queries, executes queries and return count
```
@Modifying
@Query("update")
int update....();
```

### JPA Named Queries
Two ways to handle that. First add a method
```
List<TicketPrice> namedFindTickets(@Param("name") String name)
```
Second add a query annotation
```
@Query(name="namedquery name")
sgnature(@Param("name") name)
```
## Paging and Sorting
Define query return page
```
@Query("select s from Session where s.sessionName like %:name")
Page<Model> queryByPriceRangeAndWoodType(@Param("name") String name
											 Pageable page);
```
testing in the client
```
repo.queryByPriceRangeAndWoodType("S", PageRequest.of(1, 5, Sort.by(Sort.Direction.DESC, sessionLength)))
```
give me one page with 5 result, sort by session length