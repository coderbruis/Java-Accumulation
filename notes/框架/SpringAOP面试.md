
# 1. 基础概念

## 1.1 描述一下SpringAOP

Spring AOP(Aspect Oriented Programming，面向切面编程)是OOPs(面向对象编程)的补充，它也提供了模块化。在面向对象编程中，关键的单元是对象，AOP的关键单元是切面，或者说关注点（可以简单地理解为你程序中的独立模块）。一些切面可能有集中的代码，但是有些可能被分散或者混杂在一起，例如日志或者事务。这些分散的切面被称为横切关注点。一个横切关注点是一个可以影响到整个应用的关注点，而且应该被尽量地集中到代码的一个地方，例如事务管理、权限、日志、安全等。
AOP让你可以使用简单可插拔的配置，在实际逻辑执行之前、之后或周围动态添加横切关注点。这让代码在当下和将来都变得易于维护。如果你是使用XML来使用切面的话，要添加或删除关注点，你不用重新编译完整的源代码，而仅仅需要修改配置文件就可以了。
Spring AOP通过以下两种方式来使用。但是最广泛使用的方式是Spring AspectJ 注解风格(Spring AspectJ Annotation Style)

- 使用AspectJ 注解风格
- 使用Spring XML 配置风格

## 1.2 在Spring AOP中关注点和横切关注点有什么不同

关注点是我们想在应用的模块中实现的行为。关注点可以被定义为：我们想实现以解决特定业务问题的方法。比如，在所有电子商务应用中，不同的关注点（或者模块）可能是库存管理、航运管理、用户管理等。
横切关注点是贯穿整个应用程序的关注点。像日志、安全和数据转换，它们在应用的每一个模块都是必须的，所以他们是一种横切关注点。

## 1.3 AOP有哪些可用的实现

基于Java的主要AOP实现有：

1. AspectJ
2. Spring AOP
3. JBoss AOP

## 1.4 Spring中有哪些不同的通知类型

通知(advice)是你在你的程序中想要应用在其他模块中的横切关注点的实现。Advice主要有以下5种类型：

1. 前置通知(Before Advice): 在连接点之前执行的Advice，不过除非它抛出异常，否则没有能力中断执行流。使用 @Before 注解使用这个Advice。
2. 返回之后通知(After Retuning Advice): 在连接点正常结束之后执行的Advice。例如，如果一个方法没有抛出异常正常返回。通过 @AfterReturning 关注使用它。
3. 抛出（异常）后执行通知(After Throwing Advice): 如果一个方法通过抛出异常来退出的话，这个Advice就会被执行。通用 @AfterThrowing 注解来使用。
4. 后置通知(After Advice): 无论连接点是通过什么方式退出的(正常返回或者抛出异常)都会执行在结束后执行这些Advice。通过 @After 注解使用。
5. 围绕通知(Around Advice): 围绕连接点执行的Advice，就你一个方法调用。这是最强大的Advice。通过 @Around 注解使用。

## 1.5 Spring AOP 代理是什么？

代理是使用非常广泛的设计模式。简单来说，代理是一个看其他像另一个对象的对象，但它添加了一些特殊的功能。
Spring AOP是基于代理实现的。AOP 代理是一个由 AOP 框架创建的用于在运行时实现切面协议的对象。
Spring AOP默认为 AOP 代理使用标准的 JDK 动态代理。这使得任何接口（或者接口的集合）可以被代理。Spring AOP 也可以使用 CGLIB 代理。这对代理类而不是接口是必须的。
如果业务对象没有实现任何接口那么默认使用CGLIB。

总结：

- JDK动态代理适合于接口形式的AOP
- CGLIB代理适合于没有任何接口的
- 每个Bean都会被JDK或者Cglib代理。取决于是否有接口。


## 1.6 连接点(Joint Point)和切入点(Point cut)是什么？

 连接点是程序执行的一个点。例如，一个方法的执行或者一个异常的处理。在 Spring AOP 中，一个连接点总是代表一个方法执行。举例来说，所有定义在你的 EmpoyeeManager 接口中的方法都可以被认为是一个连接点，如果你在这些方法上使用横切关注点的话。
 切入点(切入点)是一个匹配连接点的断言或者表达式。Advice 与切入点表达式相关联，并在切入点匹配的任何连接点处运行（比如，表达式 execution(* EmployeeManager.getEmployeeById(...)) 可以匹配 EmployeeManager 接口的 getEmployeeById() ）。由切入点表达式匹配的连接点的概念是 AOP 的核心。Spring 默认使用 AspectJ 切入点表达式语言。
 
 ## 1.7 什么是织入(weaving)？
 
 Spring AOP 框架仅支持有限的几个 AspectJ 切入点的类型，它允许将切面运用到在 IoC 容器中声明的 bean 上。如果你想使用额外的切入点类型或者将切面应用到在 Spring IoC 容器外部创建的类，那么你必须在你的 Spring 程序中使用 AspectJ 框架，并且使用它的织入特性。
 织入是将切面与外部的应用类型或者类连接起来以创建通知对象(adviced object)的过程。这可以在编译时(比如使用 AspectJ 编译器)、加载时或者运行时完成。Spring AOP 跟其他纯 Java AOP 框架一样，只在运行时执行织入。在协议上，AspectJ 框架支持编译时和加载时织入。
 AspectJ 编译时织入是通过一个叫做 ajc 特殊的 AspectJ 编译器完成的。它可以将切面织入到你的 Java 源码文件中，然后输出织入后的二进制 class 文件。它也可以将切面织入你的编译后的 class 文件或者 Jar 文件。这个过程叫做后编译时织入(post-compile-time weaving)。在 Spring IoC 容器中声明你的类之前，你可以为它们运行编译时和后编译时织入。Spring 完全没有被包含到织入的过程中。更多关于编译时和后编译时织入的信息，请查阅 AspectJ 文档。
 AspectJ 加载时织入(load-time weaving, LTW)在目标类被类加载器加载到JVM时触发。对于一个被织入的对象，需要一个特殊的类加载器来增强目标类的字节码。AspectJ 和 Spring 都提供了加载时织入器以为类加载添加加载时织入的能力。你只需要简单的配置就可以打开这个加载时织入器。
 
 ## 1.8 什么是动态代理？什么是静态代理？二者有什么区别？
 
 对于动态代理和静态代理总结为如下几点（网上搜的，还没具体去研究）
 
 1. 代理分为静态代理和动态代理两种。
 2. 静态代理，代理类需要自己编写代码写成。
 3. 动态代理，代理类通过 Proxy.newInstance() 方法生成。
 4. 不管是静态代理还是动态代理，代理与被代理者都要实现两样接口，它们的实质是面向接口编程。
 5. 静态代理和动态代理的区别是在于要不要开发者自己定义 Proxy 类。
 6. 动态代理通过 Proxy 动态生成 proxy class，但是它也指定了一个 InvocationHandler 的实现类。
 7. 代理模式本质上的目的是为了增强现有代码的功能。
 
 # 2. 底层原理
 
 ## 2.1 
 