Bean的装配可以理解为依赖关系注入，Bean的装配方式也就是Bean的依赖注入方式，基于xml装配Bean共有两种方式

1. 构造注入

   通过含参构造器来注入

2. 属性注入

   需要为声明一个无参构造器以及要注入的属性的setter方法

### 构造注入

声明一个Person2类，在同一个类中使用两种注入方式，所以添加无参构造器和setter（属性注入）以及有参构造器（构造注入）

**Person2:**

```java
package com.nansl.study;

public class Person2 {
    private String name;

    private int age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public Person2(){}

    public Person2(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person2{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}

```

xml配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="person4" class="com.nansl.study.Person2">
        <property name="name" value="zhangsan" />
        <property name="age" value="18" />
    </bean>

    <bean id="person5" class="com.nansl.study.Person2">
        <constructor-arg index="0" value="lisi" />
        <constructor-arg index="1" value="20" />
    </bean>

</beans>
```

测试类：

```java
package com.nansl.study;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-config.xml");
        Person2 person1 = (Person2) applicationContext.getBean("person4");
        Person2 person2 = (Person2) applicationContext.getBean("person5");
        System.out.println(person1);
        System.out.println(person2);
    }
}

```

测试结果：

```
Person2{name='zhangsan', age=18}
Person2{name='lisi', age=20}
```

> 经过在xml配置后，我们只需使用`getBean()`方法，对象实例中就已经为字段赋值了。



