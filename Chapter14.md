# Persistence with JPA

## What is ORM?

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/a4064867-536a-46be-abc4-ec2a34da39f8)

ORM sets the mapping between the set of objects which are written in the preferred programming language like JavaScript and relational database like SQL. It hides and encapsulates the SQL queries into objects and instead of SQL queries we can directly use the objects to implement the SQL query.

## What is JPA?

The Java Persistence API (JPA) is a specification of Java. It is used to persist data between Java object and relational database. JPA acts as a bridge between object-oriented domain models and relational database systems.

As JPA is just a specification, it does not perform any operation by itself. It requires an implementation. So, ORM tools like Hibernate, TopLink and iBatis implements JPA specifications for data persistence.

## What is Hibernate?

Hibernate is a Java framework that simplifies the development of Java application to interact with the database. It is an open source, lightweight, ORM (Object Relational Mapping) tool. Hibernate implements the specifications of JPA (Java Persistence API) for data persistence.

## What is Spring Boot JPA?

Spring Boot JPA is a Java specification for managing relational data in Java applications. It allows us to access and persist data between Java object/ class and relational database. 

JPA follows Object-Relation Mapping (ORM). It is a set of interfaces. It also provides a runtime EntityManager API for processing queries and transactions on the objects against the database. 

It uses a platform-independent object-oriented query language JPQL (Java Persistent Query Language).

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/c50ead3a-c377-4ebe-80ca-a2c47ccd5fb1)

1. Entity
   - An entity is a persistence domain object. Each Entity represents a table in a relational database, and each entity instance corresponds to a row in that table.
   - JPA uses Annotations or XML to map entities to a Relational database.
   - The persistent state of an entity fields or properties (setter and getter methods) use object/relational mapping annotations to map the entities and entity relationships to the relational data in the underlying data store.

2. Persistence Unit
   - A persistence unit defines a set of all entity classes that are managed by EntityManager instances in an application.
   - This set of entity classes represents the data contained within a single data store.
   - Persistence units are defined by the persistence.xml configuration file.
   - JPA uses the persistence.xml file to create the connection and setup the required environment. Provides information which is necessary for making database connections.
  
3. Persistence Class
   - Persistence (javax.persistence.Persistence) class contains java static methods to get EntityManagerFactory instances.
   - Since JPA 2.1 you can generatedatabase schemas and/or tables and/or create DDL scripts as determined by the supplied properties.
     
4. EntityManagerFactory
   - EntityManagerFactory (javax.persistence.EntityManagerFactory) class is a factory for EntityManagers.
   - During the application startup time EntityManagerFactory is created with the help of Persistence-Unit.
   - Typically, EntityManagerFactory is created once (One EntityManagerfactory object per Database) and kept alive for later use.
     
5. EntityManager
   - EntityManager is an interface to perform main actual database interactions.
     
6. Persistence Context: A persistence context handles a set of entities which hold data to be persisted in some persistence store (e.g. database).
   - Each EntityManager instance is associated with a PersistenceContext.
     
7. EntityTransaction
   - A transaction is a set of operations that either fail or succeed as a unit.
   - A database transaction consists of a set of SQL operations that are committed or rolled back as a single unit
   - Any kind of modifications initiated via EntityManager object are placed within a transaction. An EntityManager object helps creating an EntityTransaction

![image](https://github.com/asmalizaa/smvcwa/assets/23090837/6b759a76-8872-4016-a781-24fdbfba5b2e)

## Example of Spring Data JPA

Reference: (https://bezkoder.com/spring-boot-postgresql-example/)

1. Use Spring Initializr to create a new project with below settings.

   - Project: Maven
   - Language: Java
   - Spring Boot: default
   - Group: com.example
   - Artifact: jpademo
   - Name: jpademo
   - Description: Demo project for Spring Data JPA
   - Package name: com.example.jpademo
   - Packaging: Jar
   - Java: 21
   - Dependencies: Spring Web, Spring Data JPA, PostgreSQL Driver, Spring Boot DevTools
   
   Click Generate. Extract the downloaded zip file then import it into Eclipse.

2. Connect to PostgreSQL database.

   Add below configurations into application.properties file

   ```
   spring.datasource.url= jdbc:postgresql://localhost:5432/testdb
   spring.datasource.username= postgres
   spring.datasource.password= 123

   spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation= true
   spring.jpa.properties.hibernate.dialect= org.hibernate.dialect.PostgreSQLDialect

   # Hibernate ddl auto (create, create-drop, validate, update)
   spring.jpa.hibernate.ddl-auto= update
   ```
   
3. Create JPA entity class to represent a table.

   Our Data model is Tutorial with four fields: id, title, description, published.

   ```java
   package com.example.jpademo;
   
   import jakarta.persistence.Column;
   import jakarta.persistence.Entity;
   import jakarta.persistence.GeneratedValue;
   import jakarta.persistence.GenerationType;
   import jakarta.persistence.Id;
   import jakarta.persistence.Table;

   @Entity
   @Table(name = "tutorials")
   public class Tutorial {

       @Id
       @GeneratedValue(strategy = GenerationType.AUTO)
       private long id;

       @Column(name = "title")
       private String title;

       @Column(name = "description")
       private String description;

       @Column(name = "published")
       private boolean published;

       public Tutorial() {
       }

       public Tutorial(String title, String description, boolean published) {
           this.title = title;
           this.description = description;
           this.published = published;
       }

       public long getId() {
           return id;
       }

       public String getTitle() {
           return title;
       }

       public void setTitle(String title) {
           this.title = title;
       }

       public String getDescription() {
           return description;
       }

       public void setDescription(String description) {
           this.description = description;
       }

       public boolean isPublished() {
           return published;
       }

       public void setPublished(boolean isPublished) {
           this.published = isPublished;
       }

       @Override
       public String toString() {
           return "Tutorial [id=" + id + ", title=" + title + ", desc=" + description + ", published=" + published + "]";
       }
   }
   ```

4. Create a CRUD Repository interface for JPA entity.

   Let us create a repository to interact with Tutorials from the database.

   ```java
   package com.example.springbootjpademo;

   import java.util.List;
   import org.springframework.data.jpa.repository.JpaRepository;

   public interface TutorialRepository extends JpaRepository<Tutorial, Long> {

       List<Tutorial> findByPublished(boolean published);
       List<Tutorial> findByTitleContaining(String title);
   }
   ```

5. Using Spring JPA Query Method.

   Now we can use JpaRepository’s methods: save(), findOne(), findById(), findAll(), count(), delete(), deleteById()… without implementing these methods.

6. Using CRUD Repository interface in REST Controller.

   Finally, we create a controller that provides APIs for creating, retrieving, updating, deleting and finding Tutorials.

   ```java
   package com.example.springbootjpademo;

   import java.util.ArrayList;
   import java.util.List;
   import java.util.Optional;

   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.http.HttpStatus;
   import org.springframework.http.ResponseEntity;
   import org.springframework.web.bind.annotation.DeleteMapping;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.PathVariable;
   import org.springframework.web.bind.annotation.PostMapping;
   import org.springframework.web.bind.annotation.PutMapping;
   import org.springframework.web.bind.annotation.RequestBody;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RequestParam;
   import org.springframework.web.bind.annotation.RestController;

   @RestController
   @RequestMapping("/api")
   public class TutorialController {

       @Autowired
       TutorialRepository tutorialRepository;

       @GetMapping("/tutorials")
       public ResponseEntity<List<Tutorial>> getAllTutorials(@RequestParam(required = false) String title) {
           try {
               List<Tutorial> tutorials = new ArrayList<Tutorial>();

               if (title == null) {
                   tutorialRepository.findAll().forEach(tutorials::add);
               } else {
                   tutorialRepository.findByTitleContaining(title).forEach(tutorials::add);
               }

               if (tutorials.isEmpty()) {
                   return new ResponseEntity<>(HttpStatus.NO_CONTENT);
               }

               return new ResponseEntity<>(tutorials, HttpStatus.OK);
           } catch (Exception e) {
               return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
           }
       }

       @GetMapping("/tutorials/{id}")
       public ResponseEntity<Tutorial> getTutorialById(@PathVariable("id") long id) {
           Optional<Tutorial> tutorialData = tutorialRepository.findById(id);

           if (tutorialData.isPresent()) {
               return new ResponseEntity<>(tutorialData.get(), HttpStatus.OK);
           } else {
               return new ResponseEntity<>(HttpStatus.NOT_FOUND);
           }
       }

       @PostMapping("/tutorials")
       public ResponseEntity<Tutorial> createTutorial(@RequestBody Tutorial tutorial) {
           try {
               Tutorial _tutorial = tutorialRepository.save(new Tutorial(tutorial.getTitle(), tutorial.getDescription(), false));
               return new ResponseEntity<>(_tutorial, HttpStatus.CREATED);
           } catch (Exception e) {
               return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
           }
       }

       @PutMapping("/tutorials/{id}")
       public ResponseEntity<Tutorial> updateTutorial(@PathVariable("id") long id, @RequestBody Tutorial tutorial) {
           Optional<Tutorial> tutorialData = tutorialRepository.findById(id);

           if (tutorialData.isPresent()) {
               Tutorial _tutorial = tutorialData.get();
               _tutorial.setTitle(tutorial.getTitle());
               _tutorial.setDescription(tutorial.getDescription());
               _tutorial.setPublished(tutorial.isPublished());
               return new ResponseEntity<>(tutorialRepository.save(_tutorial), HttpStatus.OK);
           } else {
               return new ResponseEntity<>(HttpStatus.NOT_FOUND);
           }
       }

       @DeleteMapping("/tutorials/{id}")
       public ResponseEntity<HttpStatus> deleteTutorial(@PathVariable("id") long id) {
           try {
               tutorialRepository.deleteById(id);
               return new ResponseEntity<>(HttpStatus.NO_CONTENT);
           } catch (Exception e) {
               return new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR);
           }
       }

       @DeleteMapping("/tutorials")
       public ResponseEntity<HttpStatus> deleteAllTutorials() {
           try {
               tutorialRepository.deleteAll();
               return new ResponseEntity<>(HttpStatus.NO_CONTENT);
           } catch (Exception e) {
               return new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR);
           }
       }

       @GetMapping("/tutorials/published")
       public ResponseEntity<List<Tutorial>> findByPublished() {
           try {
               List<Tutorial> tutorials = tutorialRepository.findByPublished(true);
               if (tutorials.isEmpty()) {
                   return new ResponseEntity<>(HttpStatus.NO_CONTENT);
               }
               return new ResponseEntity<>(tutorials, HttpStatus.OK);
           } catch (Exception e) {
               return new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR);
           }
       }
   }
   ```

7. Run and test the application using Postman.

    - Get all tutorials.

      ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/7c54a6f4-b213-42f5-a35d-4d4e0552e783)

      Verify that table tutorials are created automatically.

      ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/ea16c3f0-1f76-488d-b758-4435f7843a96)

    - Try to insert a new tutorial record by sending a POST request.

      ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/00d416c4-7883-4597-b532-c0be06a70ef6)

      Verify that the new record is added into table tutorials.

      ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/56fd1101-ff43-41de-a89c-58df9b953940)

   - Try to add another few records into the table and verify.

     ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/bea4f6c4-e870-4f11-96b6-06b72cb3590d)

   - Try to update existing record.

     ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/7b8e1dd1-45df-4fa5-9dce-8e3caed96543)

     Verify the update in table tutorials.

     ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/106e17f0-871f-4595-8953-1174688596a7)

   - Get all tutorial records.

     ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/a3f40329-b03f-4c9b-afa2-4f647734e1ce)

   - Get a record by id.

     ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/8471b5e8-2ff9-4f3f-bf1c-8368c0914980)

   - Find all published tutorials.

     ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/dc5d381d-9932-4f09-91c5-4ec2e488f935)

   - Find all tutorials which title contains ‘ring’.

     ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/8fef9628-0d05-4eb5-9d4e-97a0744f9577)

   - Delete a tutorial record.

     ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/16bdaf40-f20a-4cc5-bf2a-b21f216dc6e2)

     Verify record deleted in database.

     ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/5ae8caa5-0207-45fb-aff2-17ccd4a638ec)

   - Finally, delete all records in table.

     ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/8f0e8927-1ea3-415f-97a7-47bd15252b44)

   - Verify table is now empty.

     ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/1a39fd7c-91ca-407c-a67b-1854f9a1dde1)













     




        

   




