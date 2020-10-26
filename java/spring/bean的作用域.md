bean的作用域共有五种：

- singleton
  - 单例模式，spring容器中只会有该bean的一个实例，spring会接管该bean的生命周期

- prototype
  - 每次重新`getBean()`都会重新生成一个bean的实例，spring不会接管该bean的生命周期
- request
  - 在一次HTTP请求中，会返回同一个bean实例，对于不同的HTTP请求，会返回不同的bean实例
- session
  - 在一次HTTP session中，会返回同一个bean实例
- global session
  - 在一个全局的session中，会返回同一个bean实例

### singleton

声明一个Person1类：

```java
package com.nansl.study;

public class Person1 {
}

```

bean的声明需要指定scope为singleton：

```xml
<!-- 方式一: 构造方法实例化 -->
<bean id="person1" class="com.nansl.study.Person1" scope="singleton"/>
```

测试类：

```java
package com.nansl.study;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-config.xml");
        Person1 person1 = (Person1) applicationContext.getBean("person1");
        Person1 person2 = (Person1) applicationContext.getBean("person1");
        System.out.println(person1);
        System.out.println(person2);
    }
}
```

输出结果：

```
com.nansl.study.Person1@2038ae61
com.nansl.study.Person1@2038ae61
```

> 可以看到，声明了两个对象`person1`和`person2`，但是他们的地址是相同的

### prototype

配置文件中的bean定义为：

```xml
<!-- 方式一: 构造方法实例化 -->
<bean id="person1" class="com.nansl.study.Person1" scope="prototype"/>
```

测试类：

```java
package com.nansl.study;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-config.xml");
        Person1 person1 = (Person1) applicationContext.getBean("person1");
        Person1 person2 = (Person1) applicationContext.getBean("person1");
        System.out.println(person1);
        System.out.println(person2);
    }
}
```

输出结果：

```
com.nansl.study.Person1@1e127982
com.nansl.study.Person1@60c6f5b
```

> 两次声明的对象person1和person2不相同