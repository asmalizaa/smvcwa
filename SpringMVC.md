# Spring MVC

Reference: (https://www.baeldung.com/spring-mvc-tutorial)

## What Is Spring MVC?

As the name suggests, it’s a module of the Spring framework dealing with the Model-View-Controller or MVC pattern. It combines all the advantages of the MVC pattern with the convenience of Spring.

Spring implements MVC with the front controller pattern using its DispatcherServlet.

In a nutshell, the DispatcherServlet acts as the main controller to route requests to their intended destination. Model is nothing but the data of our application, and the View is represented by any of the various template engines.

## Spring MVC Using Java Configuration

To enable Spring MVC support through a Java configuration class, we just add the @EnableWebMvc annotation:

```java
@EnableWebMvc
@Configuration
public class WebConfig {
    /// ...
}
```

This will set up the basic support we need for an MVC project, such as registering controllers and mappings, type converters, validation support, message converters and exception handling.

If we want to customize this configuration, we need to implement the WebMvcConfigurer interface:

```java
@EnableWebMvc
@Configuration
public class WebConfig implements WebMvcConfigurer {

   @Override
   public void addViewControllers(ViewControllerRegistry registry) {
      registry.addViewController("/").setViewName("index");
   }

   @Bean
   public ViewResolver viewResolver() {
      InternalResourceViewResolver bean = new InternalResourceViewResolver();

      bean.setViewClass(JstlView.class);
      bean.setPrefix("/WEB-INF/view/");
      bean.setSuffix(".jsp");

      return bean;
   }
}
```

In this example, we’ve registered a ViewResolver bean that will return .jsp views from the /WEB-INF/view directory.

Very important here is that we can register view controllers that create a direct mapping between the URL and the view name using the ViewControllerRegistry. This way, there’s no need for any Controller between the two.

If we want to also define and scan controller classes, we can add the @ComponentScan annotation with the package that contains the controllers:

```java
@EnableWebMvc
@Configuration
@ComponentScan(basePackages = { "com.example.controller" })
public class WebConfig implements WebMvcConfigurer {
    // ...
}
```

To bootstrap an application that loads this configuration, we also need an initializer class:

```java
public class MainWebAppInitializer implements WebApplicationInitializer {
    @Override
    public void onStartup(final ServletContext sc) throws ServletException {

        AnnotationConfigWebApplicationContext root = new AnnotationConfigWebApplicationContext();
        
        root.scan("com.example");
        sc.addListener(new ContextLoaderListener(root));

        ServletRegistration.Dynamic appServlet = 
          sc.addServlet("mvc", new DispatcherServlet(new GenericWebApplicationContext()));
        appServlet.setLoadOnStartup(1);
        appServlet.addMapping("/");
    }
}
```

Note that for versions earlier than Spring 5, we have to use the WebMvcConfigurerAdapter class instead of the interface.

## Spring MVC Using XML Configuration

Instead of the Java configuration above, we can also use a purely XML config:

```xml
<context:component-scan base-package="com.example.controller" />
<mvc:annotation-driven />    

<bean id="viewResolver" 
      class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/view/" />
        <property name="suffix" value=".jsp" />
    </bean>

    <mvc:view-controller path="/" view-name="index" />

</beans>
```

If we want to use a purely XML configuration, we’ll also need to add a web.xml file to bootstrap the application.

## Controller and Views

Let’s have a look at an example of a basic controller:

```java
@Controller
public class SampleController {
    @GetMapping("/sample")
    public String showForm() {
        return "sample";
    }
}
```

And the corresponding JSP resource is the sample.jsp file:

```jsp
<html>
   <head></head>
   <body>
      <h1>This is the body of the sample view</h1>	
   </body>
</html>
```

The JSP-based view files are located under the /WEB-INF folder of the project, so they’re only accessible to the Spring infrastructure and not by direct URL access.

## Spring MVC With Boot

Spring Boot is an addition to the Spring platform that makes it very easy to get started and create stand-alone, production-grade applications. Boot is not intended to replace Spring but to make working with it faster and easier.

### Spring Boot Starters

The new framework provides convenient starter dependencies, which are dependency descriptors that can bring in all the necessary technology for a certain functionality.

These have the advantage that we no longer need to specify a version for each dependency but instead allow the starter to manage dependencies for us.

The quickest way to get started is by adding the spring-boot-starter-parent pom.xml:

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
</parent>
```

This will take care of dependency management.

### Spring Boot Entry Point

Each application built using Spring Boot needs merely to define the main entry point.

This is usually a Java class with the main method, annotated with @SpringBootApplication:

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

This annotation adds the following other annotations:

- @Configuration marks the class as a source of bean definitions.
- @EnableAutoConfiguration tells the framework to add beans based on the dependencies on the classpath automatically.
- @ComponentScan scans for other configurations and beans in the same package as the Application class or below.

With Spring Boot, we can set up front end using Thymeleaf or JSP’s without using ViewResolver as defined in Section 3. By adding spring-boot-starter-thymeleaf dependency to our pom.xml, Thymeleaf gets enabled, and no extra configuration is necessary.
