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

