基于注解装配Bean，我们在类上添加注解将其声明为一个Bean，Bean中的依赖注入采用注解方式。

### 常用的注解如下：

1. @Component

   作用于类上，声明该类将作为一个Bean交由spring托管

2. @Repository

   作用于Dao层的类上，与`@Component`作用相同

3. @Service

   作用于Service层，与`@Component`作用相同

4. @Controller

   作用于Controller层，与`@Component`作用相同

5. @Scope

   作用于类上，用于指定Bean的作用域

6. @Autowired

   作用于Bean的属性变量、属性的setter方法、构造器函数，默认根据Bean类型进行装配

7. @Resource

   与`@Autowired`类似，不过默认根据Bean的name进行装配

   
   @Resource 中有两个重要属性：name 和 type。

   Spring 将 name 属性解析为 Bean 实例名称，type 属性解析为 Bean 实例类型。如果指定 name 属性，则按实例名称进行装配；如果指定 type 属性，则按 Bean 类型进行装配。

   如果都不指定，则先按 Bean 实例名称装配，如果不能匹配，则再按照 Bean 类型进行装配；如果都无法匹配，则抛出 NoSuchBeanDefinitionException 异常。

8. ·@Qualifier

   与`@Autowired`注解配合使用，会将默认地按照Bean的类型装配改为按照Bean的name进行装配，Bean 的实例名称由 @Qualifier 注解的参数指定。

9. @Value

   如果属性是一般类型，那么可以使用@Value来注入

### Demo

1. 创建DAO接口

   ```java
   package com.nansl.annotation;
   
   public interface PersonDao {
       public void add();
   }
   
   ```

2. 创建DAO层的实现类

   ```java
   package com.nansl.annotation;
   
   import org.springframework.stereotype.Repository;
   
   @Repository("personDao")
   public class PersonDaoImpl implements PersonDao {
       @Override
       public void add() {
           System.out.println("PersonDao add successful");
       }
   }
   
   ```

   在`PersonDaoImpl`类上声明`@Repository("personDao")`表明它是DAO层的Bean，其写法相当于xml配置文件中写：`<bean id="personDao"class="com.nansl.annotation.PersonDaoImpl"/>`。然后在`add()`方法中输出来验证是否调用了该方法。

3. 创建service层接口

   ```java
   package com.nansl.annotation;
   
   public interface PersonService {
       public void add();
   }
   
   ```

4. 创建service层的实现类

   ```java
   package com.nansl.annotation;
   
   import org.springframework.stereotype.Service;
   
   import javax.annotation.Resource;
   
   @Service("personService")
   public class PersonServiceImpl implements PersonService {
       @Resource(name = "personDao")
       PersonDao personDao;
   
       @Override
       public void add() {
           personDao.add();
           System.out.println("PersonService add successful");
       }
   }
   
   ```

   `@Service`与`@Repository`作用类似，表示这是一个service层的的Bean声明。

   `@Resource(name = "personDao")`表示注入`name = "personDao"`的Bean，相当于xml中的写法：`<property name="personDao"ref="personDao"/>`，调用personDao的`add()`方法，并且输出一句话来判断是否正常执行。

5. 创建controller层

   ```java
   package com.nansl.annotation;
   
   import org.springframework.context.annotation.Scope;
   import org.springframework.stereotype.Controller;
   
   import javax.annotation.Resource;
   
   @Controller("personController")
   @Scope("singleton")
   public class PersonController {
       @Resource(name = "personService")
       PersonService personService;
   
       public void add() {
           personService.add();
           System.out.println("personController add successful");
       }
   }
   
   ```

   `@Controller("personController")`与`@Service`和`@Repository`类似，代表这是一个controller的Bean

   `@Scope("singleton")`代表这个Bean的作用域是单例模式

   `@Resource(name = "personService")`依赖注入，注入`name = personService`的Bean

6. 配置文件需要添加context的配置：

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          http://www.springframework.org/schema/context/spring-context.xsd">
   
       <context:component-scan base-package="com.nansl.annotation" />
   
   </beans>
   ```

   配置文件中 加入了这四行：

   ```xml
   xmlns:context="http://www.springframework.org/schema/context"
   http://www.springframework.org/schema/context
   http://www.springframework.org/schema/context/spring-context.xsd
   <context:component-scan base-package="com.nansl.annotation" />
   ```

   使用 context 命名空间的 component-scan 元素进行注解的扫描，其 base-package 属性用于通知 spring 所需要扫描的目录。

7. 测试类

   ```java
   package com.nansl.annotation;
   
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;
   
   public class Main {
   
       public static void main(String[] args) {
           ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-config.xml");
           PersonController controller = (PersonController)applicationContext.getBean("personController");
           controller.add();
       }
   }
   
   ```

8. 运行结果：

   ```
   PersonDao add successful
   PersonService add successful
   personController add successful
   
   ```

   

