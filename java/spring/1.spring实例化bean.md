### spring实例化bean的三种方式

1. 构造器实例化
2. 静态工厂实例化
3. 实例工厂实例化

### 构造器实例化

最简单的声明方式，声明Person1类：

```java
package com.nansl.study;

public class Person1 {
}
```

配置文件中声明如下：

```xml
<!-- 方式一: 构造方法实例化 -->
<bean id="person1" class="com.nansl.study.Person1" />
```

### 静态工厂实例化

Person1如上，重要的是我们需要有一个工厂类，声明静态方法来声明并返回一个Person1对象

```java
package com.nansl.study;

public class MyBeanFactory {
    public static Person1 getPerson1() {
        return new Person1();
    }
}
```

配置文件中声明如下：

```xml
<!-- 方式二: 静态工厂实例化 -->
<bean id="person2" class="com.nansl.study.MyBeanFactory" factory-method="getPerson1"/>
```

### 实例工厂实例化

Person1如上，工厂类添加一个方法返回Person1对象

```java
package com.nansl.study;

public class MyBeanFactory {
    public MyBeanFactory() {
        System.out.println("MyBeanFactory init....");
    }

    public Person1 getPerson2() {
        return new Person1();
    }
}
```

配置文件声明如下：

```xml
<!-- 方式三: 实例工厂实例化 -->
<bean id="myBeanFactory" class="com.nansl.study.MyBeanFactory" />

<bean id="person3" factory-bean="myBeanFactory" factory-method="getPerson2" />
```

完全配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 方式一: 构造方法实例化 -->
    <bean id="person1" class="com.nansl.study.Person1" />

    <!-- 方式二: 静态工厂实例化 -->
    <bean id="person2" class="com.nansl.study.MyBeanFactory" factory-method="getPerson1"/>

    <!-- 方式三: 实例工厂实例化 -->
    <bean id="myBeanFactory" class="com.nansl.study.MyBeanFactory" />

    <bean id="person3" factory-bean="myBeanFactory" factory-method="getPerson2" />

</beans>
```



测试如下：

```java
package com.nansl.study;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-config.xml");
        Person1 person1 = (Person1) applicationContext.getBean("person1");
        Person1 person2 = (Person1) applicationContext.getBean("person2");
        System.out.println(person1);
        System.out.println(person2);
        System.out.println(applicationContext.getBean("person3"));
    }

}
```

```
MyBeanFactory init....
com.nansl.study.Person1@2038ae61
com.nansl.study.Person1@3c0f93f1
com.nansl.study.Person1@31dc339b
```

