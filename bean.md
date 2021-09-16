# @Bean

org.springframework.context.annotation @Target\({ElementType.METHOD,ElementType.ANNOTATION\_TYPE}\) @Retention\(RetentionPolicy.RUNTIME\) @Documented public interface Bean extends annotation.Annotation

**@Bean**

Indicates that a method produces a bean to be managed by the Spring container.

指示一个方法产生一个由 Spring 容器管理的 bean。

Overview

概述

The names and semantics of the attributes to this annotation are intentionally similar to those of the &lt;bean/&gt; element in the Spring XML schema. For example:

此注释的属性的名称和语义有意类似于 Spring XML 模式中的 &lt;bean/&gt; 元素的名称和语义。 例如：

```text
       public MyBean myBean() {
           // instantiate and configure MyBean obj
           return obj;
       }
```

Bean Names While a name attribute is available, the default strategy for determining the name of a bean is to use the name of the @Bean method.

虽然 name 属性可用，但确定 bean 名称的默认策略是使用 @Bean 方法的名称。

This is convenient and intuitive, but if explicit naming is desired, the name attribute \(or its alias value\) may be used.

这既方便又直观，但如果需要显式命名，则可以使用 name 属性（或其别名值）。

 Also note that name accepts an array of Strings, allowing for multiple names \(i.e. a primary bean name plus one or more aliases\) for a single bean.

还要注意 name 接受一个字符串数组，允许为单个 bean 使用多个名称（即一个主要 bean 名称加上一个或多个别名）。

```text
@Bean({"b1", "b2"}) // bean available as 'b1' and 'b2', but not 'myBean'
       public MyBean myBean() {
           // instantiate and configure MyBean obj
           return obj;
       }
```

Profile, Scope, Lazy, DependsOn, Primary, Order Note that the @Bean annotation does not provide attributes for profile, scope, lazy, depends-on or primary.

请注意，@Bean 注释不为配置文件、范围、惰性、依赖或主要提供属性。

Rather, it should be used in conjunction with @Scope, @Lazy, @DependsOn and @Primary annotations to declare those semantics. For example:

相反，它应该与 @Scope、@Lazy、@DependsOn 和 @Primary 注释结合使用来声明这些语义。 例如：

```text
    @Bean
       @Profile("production")
       @Scope("prototype")
       public MyBean myBean() {
           // instantiate and configure MyBean obj
           return obj;
       }
```

The semantics of the above-mentioned annotations match their use at the component class level: @Profile allows for selective inclusion of certain beans.

上述注释的语义与它们在组件类级别的使用相匹配：@Profile 允许选择性地包含某些 bean。

@Scope changes the bean's scope from singleton to the specified scope.

@Scope 将 bean 的作用域从单例更改为指定的作用域。

@Lazy only has an actual effect in case of the default singleton scope.

@Lazy 只有在默认单例范围的情况下才有实际效果。

@DependsOn enforces the creation of specific other beans before this bean will be created, in addition to any dependencies that the bean expressed through direct references, which is typically helpful for singleton startup.

@DependsOn 强制在创建此 bean 之前创建特定的其他 bean，以及 bean 通过直接引用表示的任何依赖项，这通常有助于单例启动。

@Primary is a mechanism to resolve ambiguity at the injection point level if a single target component needs to be injected but several beans match by type.

如果需要注入单个目标组件但多个 bean 按类型匹配，@Primary 是一种在注入点级别解决歧义的机制。

Additionally, @Bean methods may also declare qualifier annotations and @Order values, to be taken into account during injection point resolution just like corresponding annotations on the corresponding component classes but potentially being very individual per bean definition \(in case of multiple definitions with the same bean class\).

此外，@Bean 方法还可以声明限定符注解和 @Order 值，在注入点解析过程中被考虑，就像相应组件类上的相应注解一样，但每个 bean 定义可能非常独立（如果多个定义具有相同的豆类）。限定符在初始类型匹配后缩小候选集；在集合注入点的情况下，顺序值确定解析元素的顺序（多个目标 bean 按类型和限定符匹配）。

Qualifiers narrow the set of candidates after the initial type match;

限定符在初始类型匹配后缩小候选集；

order values determine the order of resolved elements in case of collection injection points \(with several target beans matching by type and qualifier\).

在集合注入点的情况下，顺序值确定解析元素的顺序（多个目标 bean 按类型和限定符匹配）。

NOTE: @Order values may influence priorities at injection points, but please be aware that they do not influence singleton startup order which is an orthogonal concern determined by dependency relationships and @DependsOn declarations as mentioned above.

注意：@Order 值可能会影响注入点的优先级，但请注意，它们不会影响单例启动顺序，这是由依赖关系和 @DependsOn 声明确定的正交问题，如上所述。

Also, javax.annotation.Priority is not available at this level since it cannot be declared on methods;

此外，javax.annotation.Priority 在此级别不可用，因为它不能在方法上声明；

its semantics can be modeled through @Order values in combination with @Primary on a single bean per type. @Bean Methods in @Configuration Classes

它的语义可以通过@Order 值结合@Primary 在每个类型的单个bean 上建模。

@Configuration 类中的 @Bean 方法

Typically, @Bean methods are declared within @Configuration classes.

通常，@Bean 方法在 @Configuration 类中声明。

In this case, bean methods may reference other @Bean methods in the same class by calling them directly.

在这种情况下，bean 方法可以通过直接调用它们来引用同一类中的其他 @Bean 方法。

This ensures that references between beans are strongly typed and navigable.

这确保了 bean 之间的引用是强类型和可导航的。

Such so-called 'inter-bean references' are guaranteed to respect scoping and AOP semantics, just like getBean\(\) lookups would. These are the semantics known from the original 'Spring JavaConfig' project which require CGLIB subclassing of each such configuration class at runtime.

这种所谓的“bean 间引用”保证尊重范围和 AOP 语义，就像 getBean\(\) 查找一样。这些是从原始“Spring JavaConfig”项目中已知的语义，它们需要在运行时对每个此类配置类进行 CGLIB 子类化。

As a consequence, @Configuration classes and their factory methods must not be marked as final or private in this mode. For example:

因此，@Configuration 类及其工厂方法在此模式下不得标记为 final 或 private。

```text
@Configuration
   public class AppConfig {
  
       @Bean
       public FooService fooService() {
           return new FooService(fooRepository());
       }
  
       @Bean
       public FooRepository fooRepository() {
           return new JdbcFooRepository(dataSource());
       }
  
       // ...
   }
```

@Bean Lite Mode

@Bean 精简模式

@Bean methods may also be declared within classes that are not annotated with @Configuration.

@Bean 方法也可以在没有用 @Configuration 注释的类中声明。

For example, bean methods may be declared in a @Component class or even in a plain old class.

例如，bean 方法可以在 @Component 类中声明，甚至可以在普通的旧类中声明。

In such cases, a @Bean method will get processed in a so-called 'lite' mode.

在这种情况下，@Bean方法将在一个所谓的“精简版”模式得到处理。

Bean methods in lite mode will be treated as plain factory methods by the container \(similar to factory-method declarations in XML\), with scoping and lifecycle callbacks properly applied.

精简模式下的 Bean 方法将被容器视为普通工厂方法（类似于 XML 中的工厂方法声明），并正确应用范围和生命周期回调。

The containing class remains unmodified in this case, and there are no unusual constraints for the containing class or the factory methods.

在这种情况下，包含类保持不变，并且包含类或工厂方法没有异常约束。

In contrast to the semantics for bean methods in @Configuration classes, 'inter-bean references' are not supported in lite mode. Instead, when one @Bean-method invokes another @Bean-method in lite mode, the invocation is a standard Java method invocation;

与@Configuration 类中 bean 方法的语义相反，精简模式不支持“bean 间引用”。相反，当一个@Bean-method 在 lite 模式下调用另一个 @Bean-method 时，该调用是标准的 Java 方法调用；

Spring does not intercept the invocation via a CGLIB proxy. This is analogous to inter-@Transactional method calls where in proxy mode, Spring does not intercept the invocation — Spring does so only in AspectJ mode. For example:

Spring 不会通过 CGLIB 代理拦截调用。 这类似于 @Transactional 间的方法调用，在代理模式下，Spring 不会拦截调用——Spring 只在 AspectJ 模式下这样做。 例如：

```text
@Component
   public class Calculator {
       public int sum(int a, int b) {
           return a+b;
       }
  
       @Bean
       public MyBean myBean() {
           return new MyBean();
       }
   }
```

Bootstrapping

引导

See the @Configuration javadoc for further details including how to bootstrap the container using AnnotationConfigApplicationContext and friends.

有关更多详细信息，包括如何使用 AnnotationConfigApplicationContext 和朋友引导容器，请参阅 @Configuration javadoc。

**BeanFactoryPostProcessor-returning @Bean methods**

Special consideration must be taken for @Bean methods that return Spring BeanFactoryPostProcessor \(BFPP\) types.

必须特别考虑返回 Spring BeanFactoryPostProcessor \(BFPP\) 类型的 @Bean 方法。

Because BFPP objects must be instantiated very early in the container lifecycle, they can interfere with processing of annotations such as @Autowired, @Value, and @PostConstruct within @Configuration classes.

由于 BFPP 对象必须在容器生命周期的早期实例化，因此它们可能会干扰 @Configuration 类中的 @Autowired、@Value 和 @PostConstruct 等注释的处理。

To avoid these lifecycle issues, mark BFPP-returning @Bean methods as static.

为避免这些生命周期问题，请将返回 BFPP 的 @Bean 方法标记为静态。

For example:

```text
@Bean
       public static PropertySourcesPlaceholderConfigurer pspc() {
           // instantiate, configure and return pspc ...
       }
```

By marking this method as static, it can be invoked without causing instantiation of its declaring @Configuration class, thus avoiding the above-mentioned lifecycle conflicts.

通过将此方法标记为静态，可以在不导致其声明的@Configuration 类的实例化的情况下调用它，从而避免上述生命周期冲突。

Note however that static @Bean methods will not be enhanced for scoping and AOP semantics as mentioned above.

但是请注意，如上所述，静态@Bean 方法不会针对作用域和 AOP 语义进行增强。

This works out in BFPP cases, as they are not typically referenced by other @Bean methods.

这适用于 BFPP 情况，因为它们通常不被其他 @Bean 方法引用。

As a reminder, an INFO-level log message will be issued for any non-static @Bean methods having a return type assignable to BeanFactoryPostProcessor.

提醒一下，对于具有可分配给 BeanFactoryPostProcessor 的返回类型的任何非静态 @Bean 方法，都将发出 INFO 级别的日志消息。  


