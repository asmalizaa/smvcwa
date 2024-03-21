# Interceptors

Reference: (https://jakarta.ee/learn/docs/jakartaee-tutorial/current/supporttechs/interceptors/interceptors.html)

Interceptors are used in conjunction with Jakarta EE managed classes to allow developers to invoke interceptor methods on an associated target class, in conjunction with method invocations or lifecycle events. Common uses of interceptors are logging, auditing, and profiling.

You can use interceptors with session beans, message-driven beans, and CDI managed beans. In all of these cases, the interceptor target class is the bean class.

An interceptor can be defined within a target class as an interceptor method, or in an associated class called an interceptor class. Interceptor classes contain methods that are invoked in conjunction with the methods or lifecycle events of the target class.

Interceptor classes and methods are defined using metadata annotations, or in the deployment descriptor of the application that contains the interceptors and target classes.

## Interceptor Classes


Interceptor classes may be designated with the optional jakarta.interceptor.Interceptor annotation, but interceptor classes are not required to be so annotated. An interceptor class must have a public, no-argument constructor.

The target class can have any number of interceptor classes associated with it. The order in which the interceptor classes are invoked is determined by the order in which the interceptor classes are defined in the jakarta.interceptor.Interceptors annotation. However, this order can be overridden in the deployment descriptor.

Interceptor classes may be targets of dependency injection. Dependency injection occurs when the interceptor class instance is created, using the naming context of the associated target class, and before any @PostConstruct callbacks are invoked.

## Interceptor Lifecycle

Interceptor classes have the same lifecycle as their associated target class. When a target class instance is created, an interceptor class instance is also created for each declared interceptor class in the target class. That is, if the target class declares multiple interceptor classes, an instance of each class is created when the target class instance is created. The target class instance and all interceptor class instances are fully instantiated before any @PostConstruct callbacks are invoked, and any @PreDestroy callbacks are invoked before the target class and interceptor class instances are destroyed.

## Example

Reference: (https://www.geeksforgeeks.org/spring-boot-interceptor/)


