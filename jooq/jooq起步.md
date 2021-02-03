###  如何开始

使用jooq开发的步骤一般分为三步：

1. 新建或者更新数据库/表
2. 反向生成java实体类
3. 进行业务开发

###  环境搭建

1. 数据库建表，作为生成record实体类使用

   https://github.com/k55k32/learn-jooq/blob/master/learn-jooq.sql   新建库 表

2. 新建一个maven项目，配置maven项目

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
   
       <groupId>org.example</groupId>
       <artifactId>jooqdemo2</artifactId>
       <version>1.0-SNAPSHOT</version>
   
       <properties>
           <maven.compiler.source>8</maven.compiler.source>
           <maven.compiler.target>8</maven.compiler.target>
       </properties>
   
       <dependencies>
           <dependency>
               <groupId>org.jooq</groupId>
               <artifactId>jooq</artifactId>
               <version>3.14.4</version>
           </dependency>
           <dependency>
               <groupId>org.jooq</groupId>
               <artifactId>jooq-meta</artifactId>
               <version>3.14.4</version>
           </dependency>
           <dependency>
               <groupId>org.jooq</groupId>
               <artifactId>jooq-codegen</artifactId>
               <version>3.14.4</version>
           </dependency>
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
               <version>8.0.18</version>
           </dependency>
       </dependencies>
   
       <build>
           <plugins>
               <plugin>
                   <artifactId>maven-compiler-plugin</artifactId>
                   <configuration>
                       <source>1.8</source>
                       <target>1.8</target>
                   </configuration>
               </plugin>
               <plugin>
                   <groupId>org.jooq</groupId>
                   <artifactId>jooq-codegen-maven</artifactId>
                   <version>3.14.4</version>
                   <configuration>
                       <jdbc>
                           <driver>com.mysql.cj.jdbc.Driver</driver>
                           <url>jdbc:mysql://localhost:3306/learn-jooq?serverTimezone=GMT%2B8</url>
                           <user>root</user>
                           <password></password>
                       </jdbc>
                       <generator>
                           <name>org.jooq.codegen.JavaGenerator</name>
                           <database>
                               <name>org.jooq.meta.mysql.MySQLDatabase</name>
                               <includes>.*</includes>
                               <inputSchema>learn-jooq</inputSchema>
                               <excludes></excludes>
                           </database>
                           <target>
                               <packageName>com.nansl.jooq.learn.codegen</packageName>
                               <directory>/Users/nansongling/IdeaProjects/jooqdemo2/src/main/java</directory>
                           </target>
                       </generator>
                   </configuration>
               </plugin>
           </plugins>
       </build>
   
   </project>
   ```

3. 使用maven插件根据数据库表生成java文件

   使用maven插件反向生成代码

   或者使用命令`mvn jooq-codegen:generate`

###  基础查询操作

```java
package com.nansl.jooq.learn;

import com.nansl.jooq.learn.codegen.tables.S1User;
import com.nansl.jooq.learn.codegen.tables.records.S1UserRecord;
import org.jooq.DSLContext;
import org.jooq.Record;
import org.jooq.Result;
import org.jooq.impl.DSL;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.List;

public class Main {
  public static void main(String[] args) {
    String jdbcUrl = "jdbc:mysql://localhost:3306/learn-jooq?serverTimezone=GMT%2B8";
    String jdbcUser = "root";
    String jdbcPwd = "";
    // 获取JDBC连接
    try(Connection connection = DriverManager.getConnection(jdbcUrl, jdbcUser, jdbcPwd)) {
      // 获取JOOQ执行器
      DSLContext context = DSL.using(connection);
      // fetch() 方法返回一个Result对象，该对象继承了List类，以下代码可以写为
      // List<Record> recordResult = context.select().from(S1User.S1_USER).fetch();
      Result<Record> recordResult = context.select().from(S1User.S1_USER).fetch();
      recordResult.forEach(record -> {
        Integer id = record.getValue(S1User.S1_USER.ID);
        String name = record.getValue(S1User.S1_USER.USERNAME);
        System.out.println("log id:" + id + "   log name:" + name);
      });
      // 使用into()方法可以指定Result的泛型，可以使用Record的方法
      // 通过 S1UserRecord 可以通过get方法直接获得表对象
      // 所有表的XXXRecord对象都是实现了 Record 对象的子类
      Result<S1UserRecord> s1UserRecordResult = recordResult.into(S1User.S1_USER);
      s1UserRecordResult.forEach(record ->{
        Integer id = record.getId();
        String name = record.getUsername();
        System.out.println("log id:" + id + "   log name:" + name);
      });
      // 可以直接使用fetchInto()来指定返回的泛型
      List<S1UserRecord> list = context.select().from(S1User.S1_USER).fetchInto(S1UserRecord.class);
      list.forEach(item -> {
        Integer id = item.getId();
        String name = item.getUsername();
        System.out.println("log id:" + id + " log name:" + name);
      });
      System.out.println(list.toString());
      Result<S1UserRecord> list1 = context.select().from(S1User.S1_USER).fetchInto(S1User.S1_USER);
      System.out.println(list1.toString());
    } catch (SQLException throwables) {
      throwables.printStackTrace();
    }
  }
}

```



