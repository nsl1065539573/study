使用 AspectJ 开发 AOP 通常有两种方式：

- 基于 XML 的声明式。
- 基于 Annotation 的声明式。

###  基于XML的声明式

基于XML的声明式是指在spring的配置文件中配置切面、切入点和通知，所有的声明都必须在`<aop:config>`元素中

基于XML实现AOP的demo

1. 创建切面类

   ```java
   package com.nansl.aspectJ;
   
   import javafx.beans.binding.ObjectExpression;
   import org.aspectj.lang.JoinPoint;
   import org.aspectj.lang.ProceedingJoinPoint;
   
   //切面类
   public class MyAspect {
       // 前置通知
       public void myBefore(JoinPoint joinPoint) {
           System.out.print("前置通知 目标：");
           System.out.print(joinPoint.getTarget() + "方法：");
           System.out.println(joinPoint.getSignature().getName());
       }
   
       // 后置通知
       public void myAfter(JoinPoint joinPoint) {
           System.out.println("后置通知,方法：" + joinPoint.getSignature().getName());
       }
   
       // 环绕通知
       public Object myAround(ProceedingJoinPoint joinPoint) throws Throwable {
           System.out.println("环绕之前");
           Object object = joinPoint.proceed();
           System.out.println("环绕之后");
           return object;
       }
   
       // 异常通知
       public void myException(JoinPoint joinPoint, Throwable e) {
           System.out.println("出错了：" + e.getMessage());
       }
   
       // 最终通知
       public void myFinally() {
           System.out.println("最终通知");
       }
   }
   
   ```

   可以发现切面类不需要继承类或者实现接口。环绕通知必须返回一个`Object`，且必须抛异常，接收`ProceedingJoinPoint`；异常通知可以获取到异常，使用`Throwable`类型的参数。`JoinPoint`类型的参数可以获取目标类的类名，方法名以及方法的参数等信息。

2. spring配置文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
               http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
               http://www.springframework.org/schema/aop
               http://www.springframework.org/schema/aop/spring-aop-2.5.xsd
               http://www.springframework.org/schema/context
               http://www.springframework.org/schema/context/spring-context.xsd">
       <bean id="dao" class="com.nansl.study.MyBeanFactory" factory-method="getBean"/>
   
       <bean id="goodsDao" class="com.nansl.study.MyBeanFactory" factory-method="getGoodsDao" />
   
       <!-- 目标类 -->
       <bean id="customerDao" class="com.nansl.study.CustomerDaoImpl" />
   
       <!-- 通知 -->
       <bean id="myAspect" class="com.nansl.aspectJ.MyAspect" />
   
       <aop:config>
           <aop:aspect ref="myAspect">
               <!-- 配置切入点 -->
               <aop:pointcut id="myPointCut" expression="execution(* com.nansl.study.*.*(..))"/>
               <!-- 前置通知 需要配置通知以及切入点 -->
               <aop:before method="myBefore" pointcut-ref="myPointCut"/>
               <!-- 后置通知 配置通知以及切入点 -->
               <aop:after-returning method="myAfter" pointcut-ref="myPointCut" />
               <!-- 环绕通知 -->
               <aop:around method="myAround" pointcut-ref="myPointCut" />
               <!-- 异常通知 -->
               <aop:after-throwing method="myException" throwing="e" pointcut-ref="myPointCut" />
               <!-- 最终通知 -->
               <aop:after method="myFinally" pointcut-ref="myPointCut" />
           </aop:aspect>
       </aop:config>
       
   </beans>
   ```

   通知类以及目标类都需要注册为Bean，使用`<aop:config>`标签来声明切片信息，`execution(* com.nansl.study.*.*(..))`表示增强study包下的所有方法，`method`用于指定通知的方法，`pointcut-ref`用来指定切入点

3. 测试类

   ```java
   package com.nansl.aspectJ;
   
   import com.nansl.study.CustomerDao;
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;
   
   public class Main {
       public static void main(String[] args) {
           ApplicationContext context = new ClassPathXmlApplicationContext("spring-config.xml");
           CustomerDao dao = (CustomerDao) context.getBean("customerDao");
           dao.add();
       }
   }
   
   ```

4. 测试结果

   ```
   前置通知 目标：com.nansl.study.CustomerDaoImpl@31190526方法：add
   环绕之前
   add...
   最终通知
   环绕之后
   后置通知,方法：add
   ```

   因为我们为切入点配置了所i有的通知类型，而且代码中没有异常，所以不会触发异常通知。

### 基于Annotation 的声明式

如果我们声明一个切面，就需要配置一个`<aop:aspect>`标签，如果切面逐渐增加，会使xml文件变得臃肿难以维护，所以我们经常使用注解进行AOP的声明

常用的注解：

|      名称       |                             说明                             |
| :-------------: | :----------------------------------------------------------: |
|     @Aspect     |                      用于定义一个切面。                      |
|     @Before     |           用于定义前置通知，相当于 BeforeAdvice。            |
| @AfterReturning |       用于定义后置通知，相当于 AfterReturningAdvice。        |
|     @Around     |         用于定义环绕通知，相当于MethodInterceptor。          |
| @AfterThrowing  |            用于定义抛出通知，相当于ThrowAdvice。             |
|     @After      |    用于定义最终final通知，不管是否异常，该通知都会执行。     |
| @DeclareParents | 用于定义引介通知，相当于IntroductionInterceptor（不要求掌握） |
|    @Pointcut    | 用于确定切入点，替代<br />`<aop:pointcut id="myPointCut" expression="execution(* com.nansl.study.*.*(..))"/>`<br />必须是private 无返回值 名称自定义 无参数 |

基于注解的声明式demo：

1. 声明通知

   ```java
   package com.nansl.aspectJ.annotation;
   
   import org.aspectj.lang.JoinPoint;
   import org.aspectj.lang.annotation.AfterThrowing;
   import org.aspectj.lang.annotation.Aspect;
   import org.aspectj.lang.annotation.Before;
   import org.aspectj.lang.annotation.Pointcut;
   import org.springframework.stereotype.Component;
   
   @Component
   @Aspect
   public class MyAspect {
       // 替代 <aop:pointcut id="myPointCut" expression="execution(* com.nansl.study.*.*(..))"/>
       // 要求 必须是private 无返回值 名称自定义  无参数
       @Pointcut("execution(* com.nansl.aspectJ.dao.*.*(..))")
       private void myPointCut(){}
   
       @Before("myPointCut()")
       public void before(JoinPoint joinPoint) {
           System.out.print("前置通知：");
           System.out.print(joinPoint.getTarget() + "方法：");
           System.out.println(joinPoint.getSignature().getName());
       }
   
       @AfterThrowing(value = "myPointCut()", throwing = "e")
       public void afterThrowing(JoinPoint joinPoint, Throwable e) {
           System.out.println("出错了：" + e.getMessage());
       }
   }
   
   ```

   `@Aspect`用于定义一个切面，该类作为组件使用，所以需要注册为spring的一个Bean

   `@pointcut`用于配置切入点，用来替代`<aop:pointcut id="myPointCut" expression="execution(* com.nansl.study.*.*(..))"/>`、

   `@Before`用来代替`<aop:before method="myBefore" pointcut-ref="myPointCut"/>`，默认传入value，指定切入点`myPointCut()`

   ` @AfterThrowing(value = "myPointCut()", throwing = "e")`用于声明异常通知

2. 建立目标类，采用注解形式：

   ```java
   package com.nansl.aspectJ.dao;
   
   import org.springframework.stereotype.Repository;
   
   @Repository("personDao")
   public class PersonDao {
       public void add() {
           System.out.println("Person add...");
           int i = 1/0;
       }
   }
   
   ```

3. 配置文件中配置扫描bean的包以及配置切面开启自动代理

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
               http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
               http://www.springframework.org/schema/aop
               http://www.springframework.org/schema/aop/spring-aop-2.5.xsd
               http://www.springframework.org/schema/context
               http://www.springframework.org/schema/context/spring-context.xsd">
       <!--扫描含com.mengma包下的所有注解-->
       <context:component-scan base-package="com.nansl.aspectJ"/>
       <!-- 使切面开启自动代理 -->
       <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
   </beans>
   ```

4. 编写测试类：

   ```java
   package com.nansl.aspectJ.annotation;
   
   import com.nansl.aspectJ.dao.PersonDao;
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;
   
   public class Main {
       public static void main(String[] args) {
           ApplicationContext context = new ClassPathXmlApplicationContext("spring-config.xml");
           PersonDao personDao = (PersonDao) context.getBean("personDao");
           personDao.add();
       }
   }
   
   ```

5. 测试结果：

   ```
   前置通知：com.nansl.aspectJ.dao.PersonDao@c333c60方法：add
   Person add...
   出错了：/ by zero
   ```

   

