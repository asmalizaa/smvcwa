# Validation

## Validating Form Input

Reference: (https://spring.io/guides/gs/validating-form-input)

You will build a simple Spring MVC application that takes user input and checks the input by using standard validation annotations. You will also see how to display the error message on the screen so that the user can re-enter input to make it be valid.

1. Create a new project using Spring Initialzr with below configurations.

   Project: Maven
   Language: Java
   Spring Boot: default
   Group: default
   Artifact: formvalidationdemo
   Name: formvalidationdemo
   Description: default
   Package Name: com.example.demo
   Packaging: jar
   Java: 21 (or choose the version installed on your machine)
   Dependencies: Spring Web, Spring Boot DevTools, Thymeleaf, and Validation

   Generate the project. Once the generated file downloaded, extract and import into your eclipse.

2. Create a PersonDemo Object

   The application involves validating a user’s name and age, so you first need to create a class that backs the form used to create a person.

   The following listing (from src/main/java/com/example/demo/PersonForm.java) shows how to do so:

   ```java
   package com.example.demo;

   import jakarta.validation.constraints.Min;
   import jakarta.validation.constraints.NotNull;
   import jakarta.validation.constraints.Size;

   public class PersonForm {
       @NotNull
       @Size(min=2, max=30)
       private String name;

       @NotNull
       @Min(18)
       private Integer age;

       public String getName() {
           return this.name;
       }

       public void setName(String name) {
           this.name = name;
       }

       public Integer getAge() {
           return age;
       }

       public void setAge(Integer age) {
           this.age = age;
       }

       public String toString() {
           return "Person(Name: " + this.name + ", Age: " + this.age + ")";
       }
   }
   ```

   The PersonForm class has two attributes: name and age. It is flagged with a few standard validation annotations:

   - @Size(min=2, max=30): Allows names between 2 and 30 characters long.
   - @NotNull: Does not allow a null value, which is what Spring MVC generates if the entry is empty.
   - @Min(18): Does not allow the age to be less than 18.

     In addition to that, you can also see getters and setters for name and age and a convenient toString() method.

3. Create a Web Controller

   Now that you have defined a form-backing object, it is time to create a simple web controller.

   The following listing (from src/main/java/com/example/demo/WebController.java) shows how to do so:

   ```java
   package com.example.demo;

   import org.springframework.stereotype.Controller;
   import org.springframework.validation.BindingResult;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.PostMapping;
   import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
   import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
   import jakarta.validation.Valid;

   @Controller
   public class WebController implements WebMvcConfigurer {
       @Override
       public void addViewControllers(ViewControllerRegistry registry) {
           registry.addViewController("/results").setViewName("results");
       }

       @GetMapping("/")
       public String showForm(PersonForm personForm) {
           return "form";
       }

       @PostMapping("/")
       public String checkPersonInfo(@Valid PersonForm personForm, BindingResult bindingResult) {
           if (bindingResult.hasErrors()) {
               return "form";
           }
           return "redirect:/results";
       }
   }
   ```

   This controller has a GET method and a POST method. Both methods are mapped to /.

   The showForm method returns the form template. It includes a PersonForm in its method signature so that the template can associate form attributes with a PersonForm.

   The checkPersonInfo method accepts two arguments:
   - A personForm object marked with @Valid to gather the attributes filled out in the form.
   - A bindingResult object so that you can test for and retrieve validation errors.
   
   You can retrieve all the attributes from the form, which is bound to the PersonForm object. In the code, you test for errors. If you encounter an error, you can send the user back to the original form template. In that situation, all the error attributes are displayed.

   If all of the person’s attribute are valid, it redirects the browser to the final results template.

4. Build an HTML Front End

   Now build the "main" page, as the following listing (from src/main/resources/templates/form.html) shows:

   ```html
   <!DOCTYPE HTML>
   <html xmlns:th="http://www.thymeleaf.org">
   <body>
       <form action="#" th:action="@{/}" th:object="${personForm}" method="post">
           <table>
               <tr>
                   <td>Name:</td>
                   <td><input type="text" th:field="*{name}" /></td>
                   <td th:if="${#fields.hasErrors('name')}" th:errors="*{name}">Name Error</td>
               </tr>
               <tr>
                   <td>Age:</td>
                   <td><input type="text" th:field="*{age}" /></td>
                   <td th:if="${#fields.hasErrors('age')}" th:errors="*{age}">Age Error</td>
               </tr>
               <tr>
                   <td><button type="submit">Submit</button></td>
               </tr>
           </table>
       </form>
   </body>
   </html>
   ```

   The page contains a simple form, with each of its field in a separate cell in a table. The form is geared to post to /. It is marked as being backed up by the personForm object that you saw in the GET method in the web controller. This is known as a "bean-backed form". There are two fields in the PersonForm bean, and you can see that they are tagged with th:field="*{name}" and th:field="*{age}". Next to each field is a secondary element that is used to show any validation errors.

   Finally, you have a button that submits the form. In general, if the user enters a name or age that violates the @Valid constraints, it bounces back to this page with the error message displayed. If a valid name and age is entered, the user is routed to the next web page.

   The following example (from src/main/resources/templates/results.html) shows the results page:

   ```html
   <html>
       <body>
           Congratulations! You are old enough to sign up for this site.
       </body>
   </html>
   ```

5. Run the application.

   If you visit http://localhost:8080/, you should see something like the following image:

   ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/0a556b04-6e3d-41b9-8214-e98d1526e0b4)

   The following pair of images show what happens if you enter N for the name and 15 for your age and click on Submit:

   ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/c428dc67-d0a6-43c7-ab21-5d5590335792)

   ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/f7d4c36c-48d4-4ceb-947f-d6508a7330a8)

   The preceding images show that, because the values violated the constraints in the PersonForm class, you get bounced back to the "main" page. Note that, if you click on Submit with nothing in the entry box, you get a different error, as the following image shows:

   ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/c8afc1e2-b0e7-4b4c-93a0-9f76172f4a63)

   If you enter a valid name and age, you end up on the results page, as the following image shows:

   ![image](https://github.com/asmalizaa/smvcwa/assets/23090837/a9447d9b-549e-400d-ac4d-33649d1b5e53)
   



   