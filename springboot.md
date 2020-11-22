# Some rough notes on Spring Boot

`@Bean`
An object that is instantiated, assembled, and otherwise managed by a Spring IoC container.

**Dependency**, an association between two classes (`Company`, `Address`)
**Injection**, class B will be injected nto class A (`Company(new Address)`)
Dependency Injection happens through passing parameters to the constructor or setter methods. 

## Inversion of Control (IoC)
**A process in which an objects defines its dependencies without creating them.** The job of constructing such dependencies is delegated to an IoC container. The container gets its instructions on what objects to instantiate, configure, and assemble by reading the configuration metadata provided (XML, Java annotations, or Java code).

### Traditional Approach
```
Address address = new Address("High Street", 1000);
Company company = new Company(address);
```
Managing dependencies like this with hundreds of classes across multiple packages can get hard. 

### With Beans
```
@Component
public class Company {
	// this body is the same as before
}
```
The `Address` class will become the bean in this example:
```
// Configuration class
@Configuration
// Instructs the container to look for beans in the package containing Company.class
@ComponentScan(basePackageClasses = Company.class)
public class Config {
    @Bean
    public Address getAddress() {
	return new Address("High Street", 1000);
    }
}
```
NOTE: *The `ApplicationContext` container is preferred over the `BeanFactory` container because it includes the functionality of `BeanFactory`.*
Because the bean is defined in a configuration class we need an instance of the `AnnotationConfigApplicationContext` class to build up a container
```
ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);
```
And now we can retrieve it's address dependency from anywhere
```
Company company = context.getBean("company", Company.class); // ("High Street", 1000)
```

Spring manages object instances in a container (`ApplicationContext`). Each class informs Spring about its dependencies and Spring will inject these objects (`Beans`) into each class. Every class has references to all object they require. The classes maintain low coupling this way. 

#### Standard scenario vs Inversion of Control scenario

`TextEditor` depends on the implementation of `SpellChecker`
```
public class TextEditor {
   private SpellChecker spellChecker;
   
   public TextEditor() {
      spellChecker = new SpellChecker();
   }
}
```
`SpellChecker` is implemented independently and is provided to `TextEditor`. The `SpellChecker` is being injected into `TextEditor`
```
public class TextEditor {
   private SpellChecker spellChecker;
   
   public TextEditor(SpellChecker spellChecker) {
      this.spellChecker = spellChecker;
   }
}
```
The flow of control has been "*inverted*". You have delegated control of this dependency to some external system (Spring, Beans?).

---

## Aspect Oriented Programming (AOP)

**Cross-cutting concerns**, functions that span multiple points of an application and are conceptually separate from the application's business logic (logging, declarative transactions, security, cache, etc)
OOP has classes, AOP has *aspects*. Dependency Injection decouples application objects from each other, while AOP decouples cross-cutting concerns from the objects that they affect.  

Essentially, let logging, caching etc be used throughout the application without coupling it together with the classes they're used in. These are functions that should be added or removed without affecting any of the classes they are applied to.

---

`@RequestBody <somedatatype> foo`
automatically deserializes the JSON into a Java type

## spring-boot-starter-validation
`@NotNull` 
Value for fields can be anything but `null` (e.g., "", " ", "Bob", 1)

`@NotEmpty`
Values must be *not null* and length greater than 0 (e.g., " ", "Bob", 1)

`@NotBlank`
Values must be *not null* and *trimmed* length greater than 0 (e.g., "Bob", 1)
