### IOC AOP 概念说明和应用场景说明

```
AOP是Aspect Oriented Programing的简称，面向切面编程。AOP适合于那些具有横切逻辑的应用：如性能监测，访问控制，事务管理、缓存、对象池管理以及日志记录。AOP将这些分散在各个业务逻辑中的代码通过横向切割的方式抽取到一个独立的模块中。AOP 实现的关键就在于 AOP 框架自动创建的 AOP 代理，AOP 代理则可分为静态代理和动态代理两大类，其中静态代理是指使用 AOP 框架提供的命令进行编译，从而在编译阶段就可生成 AOP 代理类，因此也称为编译时增强；而动态代理则在运行时借助于 JDK 动态代理、CGLIB 等在内存中“临时”生成 AOP 动态代理类，因此也被称为运行时增强。

代理对象的方法 = 增强处理 + 被代理对象的方法

Spring AOP 则采用运行时生成 AOP 代理类，因此无需使用特定编译器进行处理。由于 Spring AOP 需要在每次运行时生成 AOP 代理，因此性能略差一些。

AOP使用场景

AOP用来封装横切关注点，具体可以在下面的场景中使用

Authentication 权限

Caching 缓存

Context passing 内容传递

Error handling 错误处理

Lazy loading 懒加载

Debugging 调试

logging, tracing, profiling and monitoring 记录跟踪 优化 校准

Performance optimization 性能优化

Persistence 持久化

Resource pooling 资源池

Synchronization 同步

Transactions 事务

AOP相关概念

方面（Aspect）：一个关注点的模块化，这个关注点实现可能另外横切多个对象。事务管理是J2EE应用中一个很好的横切关注点例子。方面用Spring的 Advisor或拦截器实现。

连接点（Joinpoint）: 程序执行过程中明确的点，如方法的调用或特定的异常被抛出

通知（Advice）: 在特定的连接点，AOP框架执行的动作。各种类型的通知包括“around”、“before”和“throws”通知。通知类型将在下面讨论。许多AOP框架包括Spring都是以拦截器做通知模型，维护一个“围绕”连接点的拦截器链。Spring中定义了四个advice: BeforeAdvice, AfterAdvice, ThrowAdvice和DynamicIntroductionAdvice

切入点（Pointcut）: 指定一个通知将被引发的一系列连接点的集合。AOP框架必须允许开发者指定切入点：例如，使用正则表达式。 Spring定义了Pointcut接口，用来组合MethodMatcher和ClassFilter，可以通过名字很清楚的理解， MethodMatcher是用来检查目标类的方法是否可以被应用此通知，而ClassFilter是用来检查Pointcut是否应该应用到目标类上

引入（Introduction）: 添加方法或字段到被通知的类。 Spring允许引入新的接口到任何被通知的对象。例如，你可以使用一个引入使任何对象实现 IsModified接口，来简化缓存。Spring中要使用Introduction, 可有通过DelegatingIntroductionInterceptor来实现通知，通过DefaultIntroductionAdvisor来配置Advice和代理类要实现的接口

目标对象（Target Object）: 包含连接点的对象。也被称作被通知或被代理对象。POJO

AOP代理（AOP Proxy）: AOP框架创建的对象，包含通知。 在Spring中，AOP代理可以是JDK动态代理或者CGLIB代理。

织入（Weaving）: 组装方面来创建一个被通知对象。这可以在编译时完成（例如使用AspectJ编译器），也可以在运行时完成。Spring和其他纯Java AOP框架一样，在运行时完成织入。


```

###  AOP事务配置步骤  

```xml
1.配置spring的事务管理器
	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
2.配置aop全局事务管理
<aop:config>
        <aop:pointcut id="allControllerMethods"
                      expression="execution(* com..controller..*(..))"/>
        <aop:advisor advice-ref="defaultTransactionAdvice"
                     pointcut-ref="allControllerMethods"/>
    </aop:config>
    3.配置事务的通知（具体制定aop拦截到的哪些方法需要创建事务）
    
<tx:advice id="defaultTransactionAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <!--<tx:method name="*" isolation="DEFAULT"-->
                    <!--propagation="REQUIRED" no-rollback-for="java.lang.RuntimeException" timeout="100"/>-->
            <!--<tx:method name="*" read-only="true"/>-->
            <!-- 以方法为单位,指定方法应用什么事务属性 isolation:隔离级别 propagation:传播行为 read-only:是否只读 -->
            <!--PROPAGATION_REQUIRED：支持当前事务，如果当前没有事务，就新建一个事务。这是最常见的选择。-->
            <!--PROPAGATION_SUPPORTS：支持当前事务，如果当前没有事务，就以非事务方式执行。-->
            <!--PROPAGATION_MANDATORY：支持当前事务，如果当前没有事务，就抛出异常。-->
            <!--PROPAGATION_REQUIRES_NEW：新建事务，如果当前存在事务，把当前事务挂起。-->
            <!--PROPAGATION_NOT_SUPPORTED：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。-->
            <!--PROPAGATION_NEVER：以非事务方式执行，如果当前存在事务，则抛出异常。-->
            <!--PROPAGATION_NESTED：支持当前事务，如果当前事务存在，则执行一个嵌套事务，如果当前没有事务，就新建一个事务。-->
 
            <tx:method name="save*" propagation="REQUIRED" rollback-for="java.lang.Exception"/>
            <tx:method name="Borrow*" propagation="REQUIRED" rollback-for="java.lang.Exception"/>
            <tx:method name="add*" propagation="REQUIRED" rollback-for="java.lang.Exception"/>
            <tx:method name="create*" propagation="REQUIRED" rollback-for="java.lang.Exception"/>
            <tx:method name="insert*" propagation="REQUIRED" rollback-for="java.lang.Exception"/>
            <tx:method name="update*" propagation="REQUIRED" rollback-for="java.lang.Exception"/>
            <tx:method name="merge*" propagation="REQUIRED" rollback-for="java.lang.Exception"/>
            <tx:method name="del*" propagation="REQUIRED" rollback-for="java.lang.Exception"/>
            <tx:method name="remove*" propagation="REQUIRED" rollback-for="java.lang.Exception"/>
            <tx:method name="put*" propagation="REQUIRED" rollback-for="java.lang.Exception"/>
            <tx:method name="use*" propagation="REQUIRED" rollback-for="java.lang.Exception"/>
        </tx:attributes>
    </tx:advice>
```

### SpringMVC流程和工作原理

```html
1.用户发送请求至前端控制器DispatcherServlet
2.DispatcherServlet收到请求调用处理器映射器HandlerMapping。
3.处理器映射器根据请求url找到具体的处理器，生成处理器执行链HandlerExecutionChain(包括处理器对象和处理器拦截器)一并返回给DispatcherServlet。
4.DispatcherServlet根据处理器Handler获取处理器适配器HandlerAdapter执行HandlerAdapter处理一系列的操作，如：参数封装，数据格式转换，数据验证等操作
5.执行处理器Handler(Controller，也叫页面控制器)。
6.Handler执行完成返回ModelAndView
7.HandlerAdapter将Handler执行结果ModelAndView返回到DispatcherServlet
8.DispatcherServlet将ModelAndView传给ViewReslover视图解析器
9.ViewReslover解析后返回具体View
10.DispatcherServlet对View进行渲染视图（即将模型数据model填充至视图中）。
11.DispatcherServlet响应用户。
```

![](C:\Users\M1saka\Desktop\5220087-3c0f59d3c39a12dd.png)



### Spring注入方式

```java
1.构造器注入：通过将@Autowired注解放在构造器上来完成构造器注入，默认构造器参数通过类型自动装配，
	public class Test1 {  
    private MessageInterface message;  
    @Autowired //构造器注入  
    private Test1(MessageInterface message) {  
        this.message = message;  
        //省略getter和setter  
    } 
  }
2. 接口注入：通过将@Autowired注解放在构造器上来完成接口注入。
 public class Test2 {  
    @Autowired //接口注入  
    private MessageInterface messageInterface;  
    //省略getter和setter  
} 
3.方法参数注入：通过将@Autowired注解放在方法上来完成方法参数注入。

 public class Test3 {  
    private MessageInterface message;  
 
    @Autowired //setter方法注入  
    public void setMessage(MessageInterface message) {  
        this.message = message;  
    }  
    public String getMessage() {  
        return message;  
    }  
}
```

### SpringMVC注解及各自含义

```java
@Controller    （注入服务）
	被@Controller注解的类表示Web层实现，从而见到该注解就想到Web层实现，使用方式和@Component相同；
在SpringMVC中只需要使用这个标记一个类是Controller，然后使用@RequestMapping和@RequestParam等一些注解用以定义URL请求和Controller方法之间的映射，这样的Controller就能被外界访问到。此外，Controller不会直接依赖于HttpServletRequest 和HttpServletResponse 等HttpServlet 对象，他们可以通过Controller的方法参数灵活的获取到。分发处理器会扫描使用了该注解的类的方法，并检测该方法是否调用了@RequestMapping注解。@Controller只是定义了一个控制器类，而使用@RequestMapping注解的方法才是真正处理请求的处理器。然后使用@RequestMapping ( "/showView" )标记在Controller方法上，表示当请求/showView.do 的时候访问的是TestController 的showView 方法，该方法返回了一个包括Model 和View 的ModelAndView 对象。
---------------------------------------------------
@RequestMapping   
	使用 @RequestMapping 来映射 Request 请求与处理器，通过这个注解可以定义不同的处理器映射规则，即为控制器指定可以处理哪些URL请求。
	用@RequestMapping 来映射URL 到控制器类，或者是到Controller 控制器的处理方法上。当@RequestMapping 标记在Controller 类上的时候，里面使用@RequestMapping 标记的方法的请求地址都是相对于类上的@RequestMapping 而言的；当Controller 类上没有标记@RequestMapping 注解时，方法上的@RequestMapping 都是绝对路径。这种绝对路径和相对路径所组合成的最终路径都是相对于根路径“/ ”而言的
---------------------------------------------------
@RequestBody
	用于读取http请求的内容（字符串），通过springMVC提供的HttpMessageConverter接口将读取到的内容转换为json、xml等格式的数据，再转换为java对象绑定到Controller类方法的参数上。
简单点来说，就是把json格式的数据转换为java对象
	<script>
        jsonData();
        function jsonData()
        {
            $.ajax({
                url:"<%=path%>/user/jsonTest.do",
                contentType:'application/json;charset=utf-8',//设置json格式
                data:'{"username":"张三":"address":"福州"}',
                type:'post',
                success:function(data){
                    alert(data);
                },error:function(error){
                    alert(error);
                }
           })
        }
</script>
然后在后台接收一下：

@RequestMapping("/jsonTest.do")
public void jsonTest(@RequestBody User user) throws Exception
{
    System.out.println(user.toString());
}
---------------------------------------------------
@ResponseBody   
	@Responsebody 注解表示该方法的返回的结果直接写入 HTTP 响应正文（ResponseBody）中，一般在异步获取数据时使用，通常是在使用 @RequestMapping 后，返回值通常解析为跳转路径，加上 @Responsebody 后返回结果不会被解析为跳转路径，而是直接写入HTTP 响应正文中。
作用：
	用于将Controller中方法返回的对象通过适当的HttpMessageConverter转换为指定格式的数据，如：json、xml等，然后写入到response对象的body区，通过Response响应给客户端。需要注意的是，在使用此注解之后不会再走试图处理器，而是直接将数据写入到输入流中，他的效果等同于通过response对象输出指定格式的数据。
使用时机：
	返回的数据不是html标签的页面，而是其他某种格式的数据时（如json、xml等）使用，一般用来处理ajax请求；
--------------------------------------------------@ModelAttribute   
	在方法定义上使用该注解: SpringMVC在调用目标处理方法前, 会先逐个调用在方法级上标注了@ModelAttribute的方法；
	在方法的入参前使用该注解：可以从隐含对象中获取隐含的模型数据中获取对象，再将请求参数 –绑定到对象中，再传入入参将方法入参对象添加到模型中。
--------------------------------------------------@RequestParam   
	处理简单类型的绑定，用 @RequestParam 绑定 HttpServletRequest 请求参数到控制器方法参数，即在处理方法入参处使用该注解，可以把请求参数传递给请求方法。
	当没有明确指定从request 中取哪个参数时，Spring 在代码是debug 编译的情况下会默认取跟方法参数同名的参数，如果不是debug 编译的就会报错。此外，当需要从request 中绑定的参数和方法的参数名不相同的时候，也需要在@RequestParam 中明确指出是要绑定哪个参数。
	
	@RequestMapping ( "requestParam" )
   public String testRequestParam( @RequestParam(required=false) String name, @RequestParam ( "age" ) int age) {
       return "requestParam" ;
   } 
	
	在@RequestParam 中除了指定绑定哪个参数的属性value之外，还有一个属性required，它表示所指定的参数是否必须在request 属性中存在，默认是true，表示必须存在，当不存在时就会报错。在上面代码中我们指定了参数name的required的属性为false ，而没有指定age 的required 属性，这时候如果我们访问/requestParam.do而没有传递参数的时候，系统就会抛出异常，因为age 参数是必须存在的，而我们没有指定。而如果我们访问  /requestParam.do?age=1  的时候就可以正常访问，因为我们传递了必须的参数age ，而参数name是非必须的，不传递也可以。
-------------------------------------------------------------------------------------@PathVariable    绑定URL占位符到入参。
@ExceptionHandler    注解到方法上, 出现异常时会执行该方法。
@ControllerAdvice    使一个Controller成为全局的异常处理类, 类中用ExceptinHandler方法注解的方法可以处理所有Controller发生的异常。
------------------------------------------------------------------------------
@Autowired 
	它可以对类成员变量、方法以及构造函数进行标注，完成自动装配的工作。自动装配的意思就是让Spring从应用上下文中找到对应的bean的引用，并将它们注入到指定的bean。通过@Autowired注解可以完成自动装配。使用@Autowired 来消除代码Java代码里面的getter/setter与bean属性中的property。当然，getter看个人需求，如果私有属性需要对外提供的话，应当予以保留。

只要对应类型的bean有且只有一个，则会自动装配到该属性上。如果没有找到对应的bean，应用会抛出对应的异常，如果想避免抛出这个异常，则需要设置@Autowired(required=false)。不过，在应用程序设计中，应该谨慎设置这个属性，因为这会使得你必须面对NullPointerException的问题。

如果存在多个同一类型的bean，则Spring会抛出异常，表示装配有歧义，解决办法有两个： 
（1）通过@Qualifier注解指定需要的bean的ID； 
（2）通过@Resource注解指定注入特定ID的bean；

@Autowired 和 @Service("")的配合使用：

```



### springMVC和Struts2的区别

```java
1、Struts2采用Filter（StrutsPrepareAndExecuteFilter）实现，SpringMVC（DispatcherServlet）则采用Servlet实现。
2、Filter在容器启动之后即初始化；服务停止以后坠毁，晚于Servlet。Servlet在是在调用时初始化，先于Filter调用，服务停止后销毁。
3.Struts2框架是类级别的拦截，每次请求就会创建一个Action，Struts2中Action的一个方法可以对应一个url，而其类属性却被所有方法共享，SpringMVC是方法级别的拦截，一个方法对应一个Request上下文，所以方法直接基本上是独立的，独享request，response数据。而每个方法同时又何一个url对应，参数的传递是直接注入到方法中的，是方法所独有的。
```



### aop的配置（说出代码、注解或配置形式）

```java
一： XML配置AOP：
现在我们首先用xml文件配置方式来实现aop，spring aop中有好多切面、通知之类的理论知识在此不多说了，直接来实例，写一个切面类，在类中写上常见几个通知的方法，代码如下：

package com.spring.util;
import org.aspectj.lang.ProceedingJoinPoint;
public class InterceptorAop {
 
    public void doBefore() {
        System.out.println("========执行前置通知==========");
    }
    
    public void doAferReturning() {
        System.out.println("=========执行后置通知================");
    }
    
    public void doAfter() {
        System.out.println("========执行最终通知==========");
    }
    
    public void doAferThrowing() {
        System.out.println("=============执行意外通知================");
    }
    
    public Object doAround(ProceedingJoinPoint pjp) throws Throwable {
        
        System.out.println("=========进入判断方法===========");
        Object result = pjp.proceed();
        System.out.println("==========进入退出方法==========");
        return result;
    }
}
然后我们需要在spring的xml文件里配置切点类以及对应的方法，代码如下：
<bean id="aspetBean" class="com.spring.util.InterceptorAop"/>
<aop:config>
    <aop:aspect id="aspet" ref="aspetBean">
         <aop:pointcut expression="execution (* com.spring.service.impl..*.*(..))" id="cut"/>     
         <aop:before pointcut-ref="cut" method="doBefore"/>
         <aop:after-returning pointcut-ref="cut" method="doAferReturning"/>
         <aop:after pointcut-ref="cut" method="doAfter"/>
         <aop:after-throwing pointcut-ref="cut" method="doAferThrowing"/>
         <aop:around pointcut-ref="cut" method="doAround"/>
    </aop:aspect>
</aop:config>
在此大概说下上面的切入点表达式：
1、execution (* com.spring.service.impl..*.*(..))
2、execution ： 表示执行
3、第一个*号 : 表示返回值类型， *可以是任意类型
4、com.wangku.spring.service.impl :　代表扫描的包
5、..　:　代表其底下的子包也进行拦截  
6、第二个*号 : 代表对哪个类进行拦截，*代表所有类  
7、第三个*号 : 代表方法  *代表任意方法
8、(..) : 代表方法的参数有无都可以

此时配置已经起作用了，再次运行测试用例可以发下打印如下结果：

========执行前置通知==========
=========进入判断方法===========
--------------UserManager addUser-------------
==========进入退出方法==========
========执行最终通知==========
=========执行后置通知================

```

```java
二：注解配置AOP
使用注解使一切都变得简单，首先spring配置文件中加入注解和支持注释的配置，代码如下：

        <aop:aspectj-autoproxy/> 
        <context:annotation-config />
        <context:component-scan base-package="com.spring" />
最下面的一行配置是指我们要扫描的包。此时有人可能注意到了我们之前写的serviceImpl中的一句注解：@Service("manager")

此时我们的xml是支持扫描的，所以我们在spring配置文件中去掉bean的配置，值保持上面的这几行配置文件，然后在Junit里执行下面代码：

 @Test
    @SuppressWarnings("resource")
    public void testUserManager() {
        
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
        IUserManagerService userBean = (IUserManagerService) ctx.getBean("manager");
        userBean.addUser("zhaolibin", "buzhidao");
    } 
打印出了我们意料中的结果，然后我们开始用注解的方式配置aop切面类，具体代码如下：

package com.wangku.spring.util;
 
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
 
@Aspect
public class Interceptor {
    
    @Pointcut("execution (* com.wangku.spring.service.impl..*.*(..))")
    private void anyMethod() {
        System.out.println("============进入anyMethod方法==============");
    }
    
    @Pointcut("execution (* com.wangku.spring.service.test..*.*(..))")
    private void someMethod() {
        System.out.println("============进入someMethod方法==============");
    }
    
    @Before("anyMethod()")
    public void doBefore() {
        System.out.println("==========执行前置通知===============");
    }
    
    @After("someMethod()")
    public void doAfter() {
        System.out.println("===========执行最终通知==============");
    }
}
因为是切面类，所以使用@Aspect注解此类，然后用@Pointcut来指定切面表达式，此处我定义了2个切面表达式。

在spring配置文件中加入切面类的bean，如下

<bean id="interceptor" class="com.spring.util.Interceptor"/>
此时再次运行上面的Junit，会发现打印如下结果：

==========执行前置通知===============
--------------UserManager addUser-------------
注解已经执行了。接着在service下面再建立另一个test包，下面新建一个UserTestServiceImpl类进行多切面表达式的测试， 代码如下：

package com.spring.service.test;
 
import org.springframework.stereotype.Service;
 
import com.spring.model.UserManager;
import com.spring.service.IUserManagerService;
 
@Service("tester")
public class UserTestServiceImpl implements IUserManagerService{
 
    public void addUser(String userName, String password) {
        System.out.println("--------------UserTestServiceImpl addUser-------------");
    }
 
    public void updateUser(int userId, String userName, String password) {
        System.out.println("--------------UserTestServiceImpl updateUser-------------");
    }
 
    public void deleteUser(int userId) {
        System.out.println("--------------UserTestServiceImpl deleteUser-------------");
    }
 
    public UserManager findUser(int userId) {
        System.out.println("--------------UserTestServiceImpl findUser-------------");
        return null;
    }
}
此时利用execution (* com.spring.service.test..*.*(..))切面表达式来扫描我们新建的测试类，在Junit测试用例中执行以下代码：

    ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
    IUserManagerService userBean = (IUserManagerService) ctx.getBean("tester");
    userBean.addUser("zhaolibin", "buzhidao");

执行结果如下：

--------------UserTestServiceImpl addUser-------------
===========执行最终通知==============
由此可见，我们可以配置不同的切面表达式针对不同的类进行切面处理，当然通常情况下多用于权限等的控制。

spring aop的xml文件配置还有其他不同的形式，例如通过

        <aop:config>
            <aop:advisor advice-ref=""/>
        </aop:config>
```



### 常用注解标签

```
@Autowired默认按类型匹配的方式，在容器查找匹配的Bean，当有且仅有一个匹配的Bean时，Spring将其注入@Autowired标注的变量中。
@qualifier（指定注入Bean的名称）
如果容器中有一个以上匹配的Bean，则可以通过@Qualifier注解限定Bean的名称
@Scope即可，默认是"singleton"即单例，"prototype"表示原型即每次都会new一个新的出来。
@service
@controller
@repository
@component这四个会将修饰的类注册金是spring容器
@Lazy(true) 延迟加载
```



### spring中javabean的生命周期

```
在说明前可以思考一下Servlet的生命周期：实例化，初始init，接收请求service，销毁destroy；

    Spring上下文中的Bean也类似，如下

    1、实例化一个Bean－－也就是我们常说的new；

    2、按照Spring上下文对实例化的Bean进行配置－－也就是IOC注入；

    3、如果这个Bean已经实现了BeanNameAware接口，会调用它实现的setBeanName(String)方法，此处传递的就是Spring配置文件中Bean的id值

    4、如果这个Bean已经实现了BeanFactoryAware接口，会调用它实现的setBeanFactory(setBeanFactory(BeanFactory)传递的是Spring工厂自身（可以用这个方式来获取其它Bean，只需在Spring配置文件中配置一个普通的Bean就可以）；

    5、如果这个Bean已经实现了ApplicationContextAware接口，会调用setApplicationContext(ApplicationContext)方法，传入Spring上下文（同样这个方式也可以实现步骤4的内容，但比4更好，因为ApplicationContext是BeanFactory的子接口，有更多的实现方法）；

    6、如果这个Bean关联了BeanPostProcessor接口，将会调用postProcessBeforeInitialization(Object obj, String s)方法，BeanPostProcessor经常被用作是Bean内容的更改，并且由于这个是在Bean初始化结束时调用那个的方法，也可以被应用于内存或缓存技术；

    7、如果Bean在Spring配置文件中配置了init-method属性会自动调用其配置的初始化方法。

    8、如果这个Bean关联了BeanPostProcessor接口，将会调用postProcessAfterInitialization(Object obj, String s)方法、；

    注：以上工作完成以后就可以应用这个Bean了，那这个Bean是一个Singleton的，所以一般情况下我们调用同一个id的Bean会是在内容地址相同的实例，当然在Spring配置文件中也可以配置非Singleton，这里我们不做赘述。

    9、当Bean不再需要时，会经过清理阶段，如果Bean实现了DisposableBean这个接口，会调用那个其实现的destroy()方法；

    10、最后，如果这个Bean的Spring配置中配置了destroy-method属性，会自动调用其配置的销毁方法。
```

### 默认springmvc单例多例

```
spring bean作用域有以下5个：

singleton:单例模式，当spring创建applicationContext容器的时候，spring会欲初始化所有的该作用域实例，加上lazy-init就可以避免预处理；

prototype：原型模式，每次通过getBean获取该bean就会新产生一个实例，创建后spring将不再对其管理；

====下面是在web项目下才用到的===

request：搞web的大家都应该明白request的域了吧，就是每次请求都新产生一个实例，和prototype不同就是创建后，接下来的管理，spring依然在监听

session:每次会话，同上

global session:全局的web域，类似于servlet中的application

好了，上面都说了spring的controller默认是单例，那很自然就是singleton了。
如果有成员变量的话会被重复使用，解决方法是通过注解@Scope("prototype")，将其设置为多例模式
```

### 什么是单例

```
一个类只能生成一个实例，不能重复的new，之后的处理都是同一个实例对象。
```

### 注入方式有哪几种

```
构造器依赖注入： 构造器依赖注入通过容器触发一个类的构造器来实现的，该类有一系列参数，每个参数代表一个对其他类的依赖。
Setter方法注入： Setter方法注入是容器通过调用无参构造器或无参static工厂 方法实例化bean之后，调用该bean的setter方法，即实现了基于setter的依赖注入。
接口注入： 它是在一个接口中定义需要注入的信息，并通过接口完成注入。
```

### 你对IOC和AOP的理解

```
许多应用都是通过彼此间的相互合作来实现业务逻辑的，如类A要调用类B的方法，以前我们都是在类A中，通过自身new一个类B，然后在调用类B的方法，现在我们把new类B的事情交给spring来做，在我们调用的时候，容器会为我们实例化。
面向切面编程，在我们的应用中，经常需要做一些事情，但是这些事情与核心业务无关，比如，要记录所有update*方法的执行时间时间，操作人等等信息，记录到日志，
通过spring的AOP技术，就可以在不修改目标方法代码的情况下完成该需求。
```

### spring怎么返回一个字符串，用什么注解（json相关）

```
使用@responsbody注解，springmvc就不会用视图解析器去解析返回值，并直接将返回结果传给前端。
```

### 注解@Autowired和@resource的区别，后者没有指定name时会怎么样

```
@autowired是spring自带的标签，按照类型（byType）装配依赖对象，默认情况下它要求依赖对象必须存在，如果允许null值，可以设置它的required属性为false。如果我们想使用按照名称（byName）来装配，可以结合@Qualifier注解一起使用。

@resource由J2EE提供，默认ByName注入，有两个重要的属性：name和type，而Spring将@Resource注解的name属性解析为bean的名字，而type属性则解析为bean的类型。所以，如果使用name属性，则使用byName的自动注入策略，而使用type属性时则使用byType自动注入策略。如果既不制定name也不制定type属性，这时将通过反射机制使用byName自动注入策略。
@Resource装配顺序：

①如果同时指定了name和type，则从Spring上下文中找到唯一匹配的bean进行装配，找不到则抛出异常。

②如果指定了name，则从上下文中查找名称（id）匹配的bean进行装配，找不到则抛出异常。

③如果指定了type，则从上下文中找到类似匹配的唯一bean进行装配，找不到或是找到多个，都会抛出异常。

④如果既没有指定name，又没有指定type，则自动按照byName方式进行装配；如果没有匹配，则回退为一个原始类型进行匹配，如果匹配则自动装配。

@Resource的作用相当于@Autowired，只不过@Autowired按照byType自动注入。
```

### 解释MVC

```
M-V-C 即Model-View-Controller、模式-视图-控制器。是一种框架模式（不是设计模式），它强制性的使一个应用的输入、处理、输出流程按照Model、View、Controller的方式进行分离，这样一个应用被分成三个层——模型层、视图层、控制层，各自处理自己的任务。

模型(model)它是应用程序的主体部分，主要包括业务逻辑模块（web项目中的Action,dao类）和数据模块（pojo类）。模型与数据格式无关，这样一个模型能为多个视图提供数据。由于应用于模型的代码只需写一次就可以被多个视图重用，所以减少了代码的重复性 
视图(view) 用户与之交互的界面、在web中视图一般由jsp,html组成 
控制器(controller)接收来自界面的请求 并交给模型进行处理 在这个过程中控制器不做任何处理只是起到了一个连接的作用。
优点：耦合性低、重用性高、开发周期短、成本低、部署快、可维护性高。有利于软件工程化管理。
　　缺点：增加了系统结构和实现的复杂性。对于简单的界面，严格遵循MVC，使模型、视图与控制器分离，会增加结构的复杂性，并可能产生过多的更新操作，降低运行效率。

　　　　不适合小型，中等规模的应用程序。
```

### springMVC中使用ajax怎么保证从后台传入前台的是json格式

```java
正常的post请求(不包括ajax请求)在http头中的content-type为application/x-www-form-urlencoded,这时在java后台可以通过request.getParameter(name)的形式获取.但是通过原生ajax请求时,在java后台通过request.getParameter(name)的形式却无法获取到传入的参数.
原因是原生ajax请求时,在http头中的content-type为text/plain;charset=UTF-8.当请求到达tomcat服务器时,服务器只对application/x-www-form-urlencoded形式的http,post请求进行读取body体中的参数,并放到request的parameter中,对于原生ajax请求则直接忽略,不会读取body体中的参数,才导致request.getParameter(name)读取不到参数.
根据上面的分析结合自己的当前系统，发现系统封装的ajax默认content-type为application/json，那么这种方式在java后台通过request.getParameter(name)的形式是不能获取到传入的参数的！

传JSON对象
前端
function test () {
    var param = {username : "yitop"};

    $.ajax({
        timeout : 20000,
        type : "POST",
        dataType : "JSON",
        url : "/user/userRole.htm",
        data : param,
        success : function(data){
            alert(data);
        }
        //注意：这里不能加下面这行，否则数据会传不到后台
        //contentType:'application/json;charset=UTF-8',
    });
}
后端
Controller:

@RequestMapping(value = "userRole", method = RequestMethod.POST)
@ResponseBody
public List<Role> selectRoles(String username) throws WebTransException {
    
    /* 逻辑代码 */

}
------------------------------------------------------------------------
传JSON字符串+@RequestBody接收
前端
function icheckDelete(url){
    var parms = {
        list : array //这是个数组
    };

    $.ajax({
        dataType: "JSON",
        contentType:'application/json;charset=UTF-8',//关键是要加上这行
        traditional:true,//这使json格式的字符不会被转码
        data: JSON.stringify(parms),
        type: "DELETE", 
        timeout: 20000,
        url: url,
        success : function () {
            alert("删除成功！");
        },
        error : function (data){
            alert(data.responseText);
        }
    });
    
}
后端
Controller:

@RequestMapping(value = "deleteList", method = RequestMethod.DELETE)
@ResponseBody
public String delete(@RequestBody DeleteListRequest request) throws WebTransException{

    /* 逻辑代码 */

    return "success";
}
DeleteListRequest:

/**
 * @author fengzp
 * @date 16/12/15下午6:08
 * @email fengzp@gzyitop.com
 */
public class DeleteListRequest {
    List<Map<String, String>> list = new ArrayList<>();
    public DeleteListRequest() {
    }
    public List<Map<String, String>> getList() {
        return list;
    }
    public void setList(List<Map<String, String>> list) {
        this.list = list;
    }
}
```

### controller层中的方法能调用多个service层的方法吗

```
可以
```

### 事务的标签配置在哪个层中

```
service层
```

### aop是什么？它的作用？是用来增加耦合度还是降低？

```
面向切面编程，将封装好的对象切开，找出其中对多个对象产生影响的公共行为，并将其封装为一个可重用的模块，这个模块被命名为“切面”，切面将那些与业务无关，却被业务模块共同调用的逻辑提取并封装起来，在java方法调用时，AOP机制能自动进行方法拦截，允许在方法调用之前，调用后，以及执行异常时添加特点的代码来完成需要的功能。可以在任意阶段给已有的模块统一的添加功能。
用来做事务和日志等。
减少了系统中的重复代码，降低了模块间的耦合度，同时提高了系统的可维护性。
```

### 项目中用到的aop，举例

```
事务管理器用到了aop
```

### spring MVC怎么响应json格式的对象？

```
在方法上@responsbody注解，将Controller的方法返回的对象，通过HttpMessageConverter接口转换为指定格式的数据如：json,xml等，通过Response响应给客户端
```

### ssm框架的原理

```
SSM框架是spring MVC ，spring和mybatis框架的整合，是标准的MVC模式，将整个系统划分为表现层，controller层，service层，DAO层四层

使用spring MVC负责请求的转发和视图管理

spring实现业务对象管理，mybatis作为数据对象的持久化引擎

SpringMVC：

1.客户端发送请求到DispacherServlet（分发器）

2.由DispacherServlet控制器查询HanderMapping，找到处理请求的Controller

3.Controller调用业务逻辑处理后，返回ModelAndView

4.DispacherSerclet查询视图解析器，找到ModelAndView指定的视图

5.视图负责将结果显示到客户端

Spring：我们平时开发接触最多的估计就是IOC容器，它可以装载bean（也就是我们Java中的类，当然也包括service dao里面的），有了这个机制，我们就不用在每次使用这个类的时候为它初始化，很少看到关键字new。另外spring的aop，事务管理等等都是我们经常用到的。



Mybatis：mybatis是对jdbc的封装，它让数据库底层操作变的透明。mybatis的操作都是围绕一个sqlSessionFactory实例展开的。mybatis通过配置文件关联到各实体类的Mapper文件，Mapper文件中配置了每个类对数据库所需进行的sql语句映射。在每次与数据库交互时，通过sqlSessionFactory拿到一个sqlSession，再执行sql命令。

```

### 什么是事务？ AOP事务配置怎么配置

```
将一系列操作看成一个整体，一个失败则全部失败。
首先配置一个spring事务管理器，配置事务管理的通知，然后配置全局事务管理，具体制定拦截的哪些方法需要事务，
```

### 事务的特性、隔离级别、传播特性

```java
spring事务： 
什么是事务: 
事务逻辑上的一组操作,组成这组操作的各个逻辑单元,要么一起成功,要么一起失败.

事务特性（4种）: 
原子性 （atomicity）:强调事务的不可分割. 
一致性 （consistency）:事务的执行的前后数据的完整性保持一致. 
隔离性 （isolation）:一个事务执行的过程中,不应该受到其他事务的干扰 
持久性（durability） :事务一旦结束,数据就持久到数据库

如果不考虑隔离性引发安全性问题: 
脏读 :一个事务读到了另一个事务的未提交的数据 
不可重复读 :一个事务读到了另一个事务已经提交的 update 的数据导致多次查询结果不一致. 
虚幻读 :一个事务读到了另一个事务已经提交的 insert 的数据导致多次查询结果不一致.

解决读问题: 设置事务隔离级别（5种） 
DEFAULT 这是一个PlatfromTransactionManager默认的隔离级别，使用数据库默认的事务隔离级别. 
未提交读（read uncommited） :脏读，不可重复读，虚读都有可能发生 
已提交读 （read commited）:避免脏读。但是不可重复读和虚读有可能发生 
可重复读 （repeatable read） :避免脏读和不可重复读.但是虚读有可能发生. 
串行化的 （serializable） :避免以上所有读问题. 
Mysql 默认:可重复读 
Oracle 默认:读已提交

 
read uncommited：是最低的事务隔离级别，它允许另外一个事务可以看到这个事务未提交的数据。 
read commited：保证一个事物提交后才能被另外一个事务读取。另外一个事务不能读取该事物未提交的数据。 
repeatable read：这种事务隔离级别可以防止脏读，不可重复读。但是可能会出现幻象读。它除了保证一个事务不能被另外一个事务读取未提交的数据之外还避免了以下情况产生（不可重复读）。 
serializable：这是花费最高代价但最可靠的事务隔离级别。事务被处理为顺序执行。除了防止脏读，不可重复读之外，还避免了幻象读（避免三种）。

事务的传播行为 
PROPAGION_XXX :事务的传播行为 
* 保证同一个事务中 
PROPAGATION_REQUIRED 支持当前事务，如果不存在 就新建一个(默认) 
PROPAGATION_SUPPORTS 支持当前事务，如果不存在，就不使用事务 
PROPAGATION_MANDATORY 支持当前事务，如果不存在，抛出异常 
* 保证没有在同一个事务中 
PROPAGATION_REQUIRES_NEW 如果有事务存在，挂起当前事务，创建一个新的事务 
PROPAGATION_NOT_SUPPORTED 以非事务方式运行，如果有事务存在，挂起当前事务 
PROPAGATION_NEVER 以非事务方式运行，如果有事务存在，抛出异常 
PROPAGATION_NESTED 如果当前事务存在，则嵌套事务执行
```

### 配置事务时使用的标签是啥？

```java
<!--  配置事务传播特性 -->
<tx:advice id="TestAdvice" transaction-manager="transactionManager">
    <tx:attributes>
      <tx:method name="save*" propagation="REQUIRED"/>
      <tx:method name="del*" propagation="REQUIRED"/>
      <tx:method name="update*" propagation="REQUIRED"/>
      <tx:method name="add*" propagation="REQUIRED"/>
      <tx:method name="find*" propagation="REQUIRED"/>
      <tx:method name="get*" propagation="REQUIRED"/>
      <tx:method name="apply*" propagation="REQUIRED"/>
    </tx:attributes>
</tx:advice>
<!--  配置参与事务的类 -->
<aop:config>
<aop:pointcut id="allTestServiceMethod" expression="execution(* com.test.testAda.test.model.service.*.*(..))"/>
<aop:advisor pointcut-ref="allTestServiceMethod" advice-ref="TestAdvice" />
</aop:config>

1） advice（建议）的命名：由于每个模块都会有自己的Advice，所以在命名上需要作出规范，初步的构想就是模块名+Advice（只是一种命名规范）。

（2） tx:attribute标签所配置的是作为事务的方法的命名类型。

         如<tx:method name="save*" propagation="REQUIRED"/>

        其中*为通配符，即代表以save为开头的所有方法，即表示符合此命名规则的方法作为一个事务。

        propagation="REQUIRED"代表支持当前事务，如果当前没有事务，就新建一个事务。这是最常见的选择。

（3） aop:pointcut标签配置参与事务的类，由于是在Service中进行数据库业务操作，配的应该是包含那些作为事务的方法的Service类。

       首先应该特别注意的是id的命名，而且每个模块之间不同之处还在于以下一句：

       expression="execution(* com.test.testAda.test.model.service.*.*(..))"

       其中第一个*代表返回值，第二*代表service下子包，第三个*代表方法名，“（..）”代表方法参数。

（4） aop:advisor标签就是把上面我们所配置的事务管理两部分属性整合起来作为整个事务管理。

```

 

## Mybatis

### mybatis的动态拼接SQL

```xml
if标签
<if test="username != null">
           username=#{username}
</if>

if+where标签
<where>
        <if test="username != null">
           username=#{username}
        </if>
         
        <if test="username != null">
           and sex=#{sex}
        </if>
</where>
这个“where”标签会知道如果它包含的标签中有返回值的话，它就插入一个‘where’。此外，如果标签返回的内容是以AND 或OR 开头的，则它会剔除掉。

if+set标签
<set>
 <if test="username != null and username != ''">
                u.username = #{username},
 </if>
 <if test="sex != null and sex != ''">
                u.sex = #{sex}
 </if>
 </set>
它的主要功能和 where 标签元素其实是差不多的，主要是在包含的语句前输出一个 set，然后如果包含的语句是以逗号结束的话将会把该逗号忽略，如果 set 包含的内容为空的话则会出错。有了 set 元素就可以动态的更新那些修改了的字段。

choose(when,otherwise)标签
<choose>
   <when test="id !='' and id != null">
                  id=#{id}
   </when>
   <when test="username !='' and username != null">
                  and username=#{username}
   </when>
   <otherwise>
                  and sex=#{sex}
   </otherwise>
</choose>
如果 id 不为空，那么查询语句为：select * from user where  id=?
　　　　如果 id 为空，那么看username 是否为空，如果不为空，那么语句为 select * from user where  username=?;
　　　　如果 username 为空，那么查询语句为 select * from user where sex=?
　　　　
trim标签他是格式化的标记，可以完成set或者是where标记的功能
<trim prefix="where" prefixOverrides="and | or">
            <if test="username != null">
               and username=#{username}
            </if>
            <if test="sex != null">
               and sex=#{sex}
            </if>
</trim>

<trim prefix="set" suffixOverrides=",">
     <if test="username != null and username != ''">
                    u.username = #{username},     </if>
  <if test="sex != null and sex != ''">
                    u.sex = #{sex},
     </if>
</trim>
prefix：前缀　　　　　　
prefixoverride：去掉第一个and或者是or
suffix：后缀　　
suffixoverride：去掉最后一个逗号（也可以是其他的标记，就像是上面前缀中的and一样）

SQL标签
有时候可能某个 sql 语句我们用的特别多，为了增加代码的重用性，简化代码，我们需要将这些代码抽取出来，然后使用时直接调用。
<!-- 定义 sql 片段 -->
<sql id="selectUserByUserNameAndSexSQL">
    <if test="username != null and username != ''">
        AND username = #{username}
    </if>
    <if test="sex != null and sex != ''">
        AND sex = #{sex}
    </if>
</sql>

引用 sql 片段
<select id="selectUserByUsernameAndSex" resultType="user" parameterType="com.ys.po.User">
    select * from user
    <trim prefix="where" prefixOverrides="and | or">
        <!-- 引用 sql 片段，如果refid 指定的不在本文件中，那么需要在前面加上 namespace -->
        <include refid="selectUserByUserNameAndSexSQL"></include>
        <!-- 在这里还可以引用其他的 sql 片段 -->
    </trim>
</select>


```

### mybatis的循环标签

```xml
foreach标签
<where>
        <!--
            collection:指定输入对象中的集合属性
            item:每次遍历生成的对象
            open:开始遍历时的拼接字符串
            close:结束时拼接的字符串
            separator:遍历对象之间需要拼接的字符串
            select * from user where 1=1 and (id=1 or id=2 or id=3)
          -->
        <foreach collection="ids" item="id" open="and (" close=")" separator="or">
            id=#{id}
        </foreach>
    </where>
```

### 说一下mybatis的作用，以及怎样配置

```xml
MyBatis是持久层框架，它是支持JDBC的！简化了持久层开发！
MyBatis消除了几乎所有的JDBC代码。MyBatis使用简单的XML或注解用于配置和映射，将接口和JavaPOJOs(Plan Old Java Objects,普通的Java对象)映射成数据库中的记录。

导入相关jar包后，
	编写mybatis-conf.xml件
	
	<configuration>
    <environments default="development">
        <environment id="development">
        <!-- 事务策略是JDBC -->
            <transactionManager type="JDBC" />
            <!-- 数据源的方式 POOLED -->
            <dataSource type="POOLED">
                    <property name="driver" value="oracle.jdbc.OracleDriver" />
                <property name="url" value="jdbc:oracle:thin:@localhost:1521:orcl" />
                <property name="username" value="orcl" />
                <property name="password" value="orcl" />
            </dataSource>
        </environment>
        
        
    </environments>

    <!--映射文件：描述某个实体和数据库表的对应关系 -->
    <mappers>
        <mapper resource="cn/happy/entity/Dept.xml" />
    </mappers>
</configuration>
	
	
（1）environments元素：用于配置多个数据环境，这样可以映射多个数据库信息。采用default来指定默认使用哪个数据库环境。environment则是每个数据库环境的具体配置，包括连接信息和事务管理信息。

在MyBatis中有两种事务管理器类型（type="[JDBC|MANAGED]"）:

JDBC：直接使用了JDBC控制事务。它依赖于从数据源得到的连接来管理事务范围。

MANAGED：MyBatis自己不负责控制事务，它会让容器来管理事务的整个生命周期（如通过Spring或JavaEE应用服务器）。

数据源类型有三种：UNPOOLED,       POOLED,      JNDI。

UNPOOLED：这个数据源的实现是每次被请求时简单打开和关闭连接。它有一点慢，这是对简单应用程序的一个很好选择，因为它不需要及时的连接。

POOLED：采用连接池以避免创建新的连接实例时必要的初始连接和认证时间。这是一种当前Web应用程序快速响应请求的很流行的方法。

 JNDI：这个数据源的实现是为了使用如Spring或应用服务器这类的容器。容器可以集中或在外部配置数据源，MyBatis通过JNDI获取数据源。

（2）mappers元素：包含所有mapper的列表，告诉MyBatis到哪里去找SQL语句。Mapper主要是定义SQL映射文件。


之后配置sql映射文件对应dao接口的限定名
```

### Mybatis和Hibernate的区别

```
相同点

1.都是对象关系映射（Object Relational Mapping）框架，体现为都提供实体类和数据库表相互映射的功能。

2.业务逻辑代码层面的操作基本一致。

区别

1.hibernate设计理念是完全面向POJO（Plain Ordinary Java Object）的，所以使用者可以基本不用书写sql就能通过配置的映射关系完成数据库操作，但是mybatis不一样，他需要我们手动书写sql。总的来说，hibernate的优势在于能让程序开发人员更多的关注业务实现，而不是sql书写（框架会根据映射关系自动生成sql）。但是，mybatis虽然需要我们自己书写sql和接口，开发工作量较大，但是它比起hibernate更加灵活，并且能够不断的优化sql（因为是自定义的），也就是具有更强的灵活性和可优化性。

2.hibernate通常用于传统管理系统的开发，而mybatis广泛的应用于互联网开发（因为互联网需要灵活和可优化，毕竟一条sql的执行时间在优化前和优化后差别很大）
```

### Mybatis的XML文件都写什么

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <!--环境配置，连接的数据库，这里使用的是MySQL-->
    <environments default="mysql">
        <environment id="mysql">
            <!--指定事务管理的类型，这里简单使用Java的JDBC的提交和回滚设置-->
            <transactionManager type="JDBC"></transactionManager>
            <!--dataSource 指连接源配置，POOLED是JDBC连接对象的数据源连接池的实现-->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"></property>
                <property name="url" value="jdbc:mysql://127.0.0.1:3306/mybbs"></property>
                <property name="username" value="root"></property>
                <property name="password" value="root"></property>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!--这是告诉Mybatis区哪找持久化类的映射文件，对于在src下的文件直接写文件名，
            如果在某包下，则要写明路径,如：com/mybatistest/config/User.xml-->
        <mapper resource="User.xml"></mapper>
    </mappers>
</configuration>
<configuration>：Mybatis配置文件的根元素为

<environment>：用来配置Mybatis的环境，即连接的数据库。

<transationManager>：配置Mybatis的事务管理

<dataSource>：数据源，Mybatis推荐使用数据源（维持着一个连接池，而不用每次连接都1开启一个连接）来管理数据库连接。而dataSource下的属性相信你已经懂了（如果你学过JDBC的配置）
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.test.springtest.User">
    <select id="GetUserByID" parameterType="int" resultType="com.test.springtest.dao.MUser">
        select * from `student` where id = #{id}
    </select>

    <insert
            id="saveUser" parameterType="com.test.springtest.User"
            useGeneratedKeys="true">
        insert into student(NAME,AGE) values (#{name},#{age})
    </insert>
</mapper>
<mapper namespace="com.test.springtest.User"
         为这个mapper指定一个唯一的namespace，它习惯上设置为：“包名+sql映射文件名”，这样可以保值名的唯一。

<select id="GetUserByID" parameterType="int" resultType="com.test.mybatistest.User">
       id：这个select语句的id
       parameterType：指定查询是传入的参数类型
       resultType：即返回结果集的类型，这理指定为User类型

select * from `student` where id = #{id}   
       一条select语句

useGeneratedKeys="true"  
       使用数据库的自动增长策略
```



### mybatis一对多怎么实现，具体说一下

```
MyBatis中的一对多和对多一，多对多 
主要就是resultMap中 
association – 一个复杂的类型关联;许多结果将包成这种类型（一对多） 
collection – 复杂类型的集合（多对一） 
这2个属性的使用，而一对多和多对一都是相互的，只是站的角度不同。
```

```xml
一对多，由1的一方维护多的一方的关系
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cn.wxy.dao.PersonDao">
	<resultMap type="Phone" id="selectPhomap">
		<id property="id" column="phoID"/>
		<result property="phoneNum" column="phoneNum"/>
		<collection property="persons" ofType="Person">
			<id property="id" column="id"/>
			<result property="username" column="username"/>
			<result property="password" column="password"/>
			<result property="gender" column="gender"/>
			<result property="age" column="age"/>
			<result property="phoneID" column="phoneID"/>
		</collection>
	</resultMap>
	<select id="select" parameterType="int" resultMap="selectPhomap">
		select pho.id phoID, pho.phoneNum, per.* from tbl_phone pho, tbl_person per
		where pho.id=#{id}
		and pho.id=per.phoneID
	</select>
</mapper>
```

### mybatis里用到过哪些动态拼接

```
set，if，chosse（when，oherwise），foreach，where
```

### 用mybatis循环一个list具体怎么实现

```xml
使用foreach标签，colleation指定list，item起别名，使用open指定以什么开头，close指定结尾，separator去除多余的字符。
begin
		<foreach collection="list" item="item" index="index" separator="" open="" close="“, separator=";">
			insert into TABLE_OPERATION
			(FIELD1,FIELD2,FIELD3,FIELD4,FIELD5,FIELD6)
			values
				(
					#{item.field1},
					#{item.field2},
					#{item.field3},
					#{item.field4},
					#{item.field5},
					#{item.field6}
				)
 
		</foreach>
		;end;
```

### mybatis不写namespace的话会怎么样

```
报错，
```

### mybatis中select标签中一定要写的参数是什么

```
id参数用来标识，面向接口编程时要与方法名一致
返回类型或者返回的结果集
```

### 项目中mybatis常报的异常有哪些

```
BuilderException：
persistenException：
```

### #和$的区别

```
#符号：可以有效防止sql注入，在使用时不需要关心参数值的类型，mybatis会自动进行java类型和jdbc类型的转换。#{}可以接收简单类型值或pojo属性值，如果parameterType传输单个简单类型值，#{}括号中可以是value或其它名称。
$符号：可以将parameterType 传入的内容拼接在sql中且不进行jdbc类型转换， ${}可以接收简单类型值或pojo属性值，如果parameterType传输单个简单类型值，${}括号中只能是value。使用${}不能防止sql注入，但是有时用${}会非常方便，如模糊查询。
```

 

## Struts2

### strtus2框架的流程

```
①创建一个web项目，导入所需的jar包。可在官网下载
②定义一个java类 action，用于处理请求
③在src下，创建一个名为struts.xml（只能这么写）的文件，导入约束文件，然后写一个package标签，里面定义action标签，action里面定义一个result标签
package配置详解：
     package:跟项目中的包没关系，用来管理action的，
     name:给这个package起个名字
     （名字随意起，但是package跟package之间名字不能重复，
     一般情况下，命名根据当前package的分类来命名的）
     namespace:访问路径地址 前缀 跟name无关 跟其他package的namespace能不能重复 没有要求
     extends:必须继承struts-default 从struts2核心包中 struts2-core-2.3.24.jar 下的 struts-default.xml中继承
     abstract：抽象 声明的一个标志当前这个配置文件不能独立运行 等待被继承
-->

 

<!--
    action配置信息详解：
    name:给action起名字 决定了访问路径最后的地址
    class:访问了哪个类 完整路径名
    method:访问类中的哪个方法
-->
<!--
    result配置信息：
    name对应的是action类中的method的返回值 这个名字可以随便写
    但是必须要和action类中method的返回结果一样
    但是一般情况下 使用特定的

type： dispatcher  转发：转发地址栏不会变 发送一次请求 浏览器不知道转发的发生
       redirect   重定向：重定向地址栏会变 发送二次请求 浏览器知道重定向的发生
 标签中的值是跳转页面的地址
-->

include在str struts.xml可以去读取其他位置的xml配置文件

要求：新创建的xml必须也有约束

<include file="cn/hd/dynamic/struts01.xml"></include>
由于Struts2是基于过滤器的，所以还要在web.xml中配置过滤器
到此结束，
```

### struts2是单例还是多例

```
Struts2是基于类的属性进行发的，定义属性可以整个类通用。所以Struts2的Action是多实例的并非单例，也就是每次请求产生一个Action的对象。Action类中往往包含了数据属性，例如在页面填写的form表单的字段，Action中有对应的的属性来绑定页面form表单字段。显然如果Action是单实例的话，那么多线程的环境下就会相互影响，例如造成别人填写的数据被你看到了。

但是什么有人说Struts2的Action 默认是单例的？而且还可以进行配置呢？ 
因为在和Spring一起使用的时候，Action交给Spring进行管理，默认的就是单例，所以才会有人说Struts2默认是单例的。 
所以在Spring整合Struts2开发时，如果需要用使用Struts2多例，就在spring的action bean配置的时候设置scope=”prototype”。 
```

### strus2的工作流程？

```
1、客户端浏览器发出HTTP请求.
2、根据web.xml配置，该请求被FilterDispatcher接收
3、根据struts.xml配置，找到需要调用的Action类和方法， 并通过IoC方式，将值注入给Aciton
4、Action调用业务逻辑组件处理业务逻辑，这一步包含表单验证。
5、Action执行完毕，根据struts.xml中的配置找到对应的返回结果result，并跳转到相应页面
6、返回HTTP响应到客户端浏览器
```

## Maven:

### 用过maven吗？Maven的几个命令是什么？

```
1、mvn compile 编译,将Java 源程序编译成 class 字节码文件。
2、mvn test 测试，并生成测试报告
3、mvn clean 将以前编译得到的旧的 class 字节码文件删除
4、mvn pakage 打包,动态 web工程打 war包，Java工程打 jar 包。
5、mvn install 将项目生成 jar 包放在仓库中，以便别的模块调用
```

### maven作用

```
管理jar包，处理jia报的依赖关系。就是通过pom.xml文件的配置获取jar包，而不用手动去添加jar包，
```

