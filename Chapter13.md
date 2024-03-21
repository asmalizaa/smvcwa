# Persistence with JDBC

Reference: (https://www.bezkoder.com/spring-boot-jdbctemplate-postgresql-example/)

We will build a Spring Boot Rest API using Spring Data Jdbc with PostgreSQL Database for a Tutorial application that:

- Each Tutorial has id, title, description, published status.
- Apis help to create, retrieve, update, delete Tutorials.
- APIs also support custom finder methods such as find by published status or by title.
- These are APIs that we need to provide:

  <table>
      <tr><th>Methods</th><th>URLs</th><th>Actions</th></tr>
      <tr>
          <td>POST</td>
          <td>/api/tutorials</td>
          <td>create new Tutorial</td>
      </tr>
      <tr>
          <td>GET</td>
          <td>/api/tutorials</td>
          <td>retrieve all Tutorials</td>
      </tr>
      <tr>
          <td>GET</td>
          <td>/api/tutorials/:id</td>
          <td>retrive a Tutorial by :id/td>
      </tr>
      <tr>
          <td>PUT</td>
          <td>/api/tutorials/:id</td>
          <td>update a Tutorial by :id</td>
      </tr>
      <tr>
          <td>DELETE</td>
          <td>/api/tutorials/:id</td>
          <td>delete a Tutorial by :id</td>
      </tr>
      <tr>
          <td>DELETE</td>
          <td>/api/tutorials</td>
          <td>delete all Tutorials</td>
      </tr>
      <tr>
          <td>GET</td>
          <td>/api/tutorials/published</td>
          <td>find all published Tutorials</td>
      </tr>
      <tr>
          <td>GET</td>
          <td>/api/tutorials?title=[keyword]</td>
          <td>find all Tutorials which title contains keyword</td>
      </tr>
  </table>

– We make CRUD operations & finder methods with Spring Data Jdbc.
– The database will be PostgreSQL by configuring project dependency & datasource.

1. Create a new project using Spring Initialzr with below configurations.

   - Project: Maven
   - Language: Java
   - Spring Boot: default
   - Group: default
   - Artifact: jdbcdemo
   - Name: jdbcdemo
   - Description: default
   - Package Name: com.example.jdbcdemo
   - Packaging: jar
   - Java: 21 (or choose the version installed on your machine)
   - Dependencies: **Spring Web, Spring Boot DevTools, Spring Data JPA, PostgreSQL Driver**

   Generate the project. Once the generated file downloaded, extract and import into your eclipse.

2. Configure database credentials. Under src/main/resources folder, open application.properties and write these lines.

   ```
   spring.datasource.url= jdbc:postgresql://localhost:5432/testdb
   spring.datasource.username= postgres
   spring.datasource.password= 123
   ```

   **Note: Update the password to be the password that you have setup for your local database installation.**

3. The data model.

   ```java
   package com.example.jdbcdemo;

   public class Tutorial {
       private long id;
       private String title;
       private String description;
       private boolean published;

       public Tutorial() {
       }

       public Tutorial(long id, String title, String description, boolean published) {
           this.id = id;
           this.title = title;
           this.description = description;
           this.published = published;
       }

       public Tutorial(String title, String description, boolean published) {
           this.title = title;
           this.description = description;
           this.published = published;
       }

       public void setId(long id) {
           this.id = id;
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

4. Create the JDBC repository.

   Let’s create a repository to interact with Tutorials from the database.

   In repository package, create TutorialRepository interface that provides abstract methods:
   - for CRUD Operations: save, findById, findAll, update, deleteById, deleteAll.
   - custom finder methods: findByPublished, findByTitleContaining.

   ```java
   package com.example.jdbcdemo;

   import java.util.List;
   
   public interface TutorialRepository {

       int save(Tutorial book);

       int update(Tutorial book);

       Tutorial findById(Long id);

       int deleteById(Long id);

       List<Tutorial> findAll();

       List<Tutorial> findByPublished(boolean published);

       List<Tutorial> findByTitleContaining(String title);

       int deleteAll();
   }
   ```

   We continue to define JdbcTutorialRepository which implements TutorialRepository. It uses JdbcTemplate object for executing SQL queries or updates to interact with PostgreSQL Database.

   ```java
   package com.example.jdbcdemo;

   import java.util.List;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.dao.IncorrectResultSizeDataAccessException;
   import org.springframework.jdbc.core.BeanPropertyRowMapper;
   import org.springframework.jdbc.core.JdbcTemplate;
   import org.springframework.stereotype.Repository;

   @Repository
   public class JdbcTutorialRepository implements TutorialRepository {

       @Autowired
       private JdbcTemplate jdbcTemplate;

       @Override
       public int save(Tutorial tutorial) {
           return jdbcTemplate.update("INSERT INTO tutorials (title, description, published) VALUES(?,?,?)", 				new Object[] { tutorial.getTitle(), tutorial.getDescription(), tutorial.isPublished() });
       }

       @Override
       public int update(Tutorial tutorial) {
           return jdbcTemplate.update("UPDATE tutorials SET title=?, description=?, published=? WHERE id=?", new Object[] {
           tutorial.getTitle(), tutorial.getDescription(), tutorial.isPublished(), tutorial.getId() });
       }

       @Override
       public Tutorial findById(Long id) {
           try {
               Tutorial tutorial = jdbcTemplate.queryForObject("SELECT * FROM tutorials WHERE id=?",					BeanPropertyRowMapper.newInstance(Tutorial.class), id);
               return tutorial;
           } catch (IncorrectResultSizeDataAccessException e) {
               return null;
           }
       }

       @Override
       public int deleteById(Long id) {
           return jdbcTemplate.update("DELETE FROM tutorials WHERE id=?", id);
       }

       @Override
       public List<Tutorial> findAll() {
           return jdbcTemplate.query("SELECT * from tutorials", BeanPropertyRowMapper.newInstance(Tutorial.class));
       }

       @Override
       public List<Tutorial> findByPublished(boolean published) {
           return jdbcTemplate.query("SELECT * from tutorials WHERE published=?",				BeanPropertyRowMapper.newInstance(Tutorial.class), published);
       }

       @Override
       public List<Tutorial> findByTitleContaining(String title) {
           String q = "SELECT * from tutorials WHERE title ILIKE '%" + title + "%'";
           return jdbcTemplate.query(q, BeanPropertyRowMapper.newInstance(Tutorial.class));
       }

       @Override
       public int deleteAll() {
           return jdbcTemplate.update("DELETE from tutorials");
       }
   }
   ```

   – JDBCTemplate implements JdbcOperations which provides useful methods: execute(), query(), update(), queryForObject()
   – BeanPropertyRowMapper implements RowMapper that converts a table row into a new instance of the specified mapped target class (Tutorial).
   – Tutorial class must be a top-level class and have a default constructor (no-argument).

5. Create Spring Rest APIs Controller.

   Finally, we create a controller that provides APIs for creating, retrieving, updating, deleting and finding Tutorials.

   ```java
   package com.example.jdbcdemo;

   import java.util.ArrayList;
   import java.util.List;
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
           Tutorial tutorial = tutorialRepository.findById(id);

           if (tutorial != null) {
               return new ResponseEntity<>(tutorial, HttpStatus.OK);
           } else {
               return new ResponseEntity<>(HttpStatus.NOT_FOUND);
           }
       }

       @PostMapping("/tutorials")
       public ResponseEntity<String> createTutorial(@RequestBody Tutorial tutorial) {
           try {
               tutorialRepository.save(new Tutorial(tutorial.getTitle(), tutorial.getDescription(), false));
               return new ResponseEntity<>("Tutorial was created successfully.", HttpStatus.CREATED);
           } catch (Exception e) {
               return new ResponseEntity<>(null, HttpStatus.INTERNAL_SERVER_ERROR);
           }
       }

       @PutMapping("/tutorials/{id}")
       public ResponseEntity<String> updateTutorial(@PathVariable("id") long id, @RequestBody Tutorial tutorial) {
           Tutorial _tutorial = tutorialRepository.findById(id);

           if (_tutorial != null) {
               _tutorial.setId(id);
               _tutorial.setTitle(tutorial.getTitle());
               _tutorial.setDescription(tutorial.getDescription());
               _tutorial.setPublished(tutorial.isPublished());

               tutorialRepository.update(_tutorial);
               return new ResponseEntity<>("Tutorial was updated successfully.", HttpStatus.OK);
           } else {
               return new ResponseEntity<>("Cannot find Tutorial with id=" + id, HttpStatus.NOT_FOUND);
           }
       }

       @DeleteMapping("/tutorials/{id}")
       public ResponseEntity<String> deleteTutorial(@PathVariable("id") long id) {
           try {
               int result = tutorialRepository.deleteById(id);
               if (result == 0) {
                   return new ResponseEntity<>("Cannot find Tutorial with id=" + id, HttpStatus.OK);
               }
               return new ResponseEntity<>("Tutorial was deleted successfully.", HttpStatus.OK);
           } catch (Exception e) {
               return new ResponseEntity<>("Cannot delete tutorial.", HttpStatus.INTERNAL_SERVER_ERROR);
           }
       }

       @DeleteMapping("/tutorials")
       public ResponseEntity<String> deleteAllTutorials() {
           try {
               int numRows = tutorialRepository.deleteAll();
               return new ResponseEntity<>("Deleted " + numRows + " Tutorial(s) successfully.", HttpStatus.OK);
           } catch (Exception e) {
               return new ResponseEntity<>("Cannot delete tutorials.", HttpStatus.INTERNAL_SERVER_ERROR);
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

   – @RestController annotation is used to define a controller and to indicate that the return value of the methods should be be bound to the web response body.
   – @RequestMapping("/api") declares that all Apis’ url in the controller will start with /api.
   – We use @Autowired to inject TutorialRepository bean to local variable.

6. Run and test using Postman.
