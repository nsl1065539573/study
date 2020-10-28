我们也可以使用注解形式来声明事务，只需要做两件事

1. 配置文件中开启注解：

   ```xml
   <tx:annotation-driven transaction-manager="txManager" />
   ```

2. 在方法上添加事务

   ```java
   package com.nansl.service.impl;
   
   import com.nansl.dao.AccountDao;
   import com.nansl.service.AccoountService;
   import org.springframework.transaction.annotation.Isolation;
   import org.springframework.transaction.annotation.Propagation;
   import org.springframework.transaction.annotation.Transactional;
   
   public class AccountServiceImpl implements AccoountService {
       private AccountDao accountDao;
   
       public void setAccountDao(AccountDao dao) {
           this.accountDao = dao;
       }
   
       @Override
       @Transactional(isolation = Isolation.DEFAULT, propagation = Propagation.REQUIRED, readOnly = false)
       public void transfer(String outUser, String inUser, int money) {
           accountDao.out(outUser, money);
           accountDao.in(inUser, money);
       }
   }
   
   ```

3. 在上一个xml声明式中修改这两个点，运行，测试：

   ```sql
   select * from account;
   +----+----------+-------+
   | id | username | money |
   +----+----------+-------+
   |  1 | zhangsan |   800 |
   |  2 | lisi     |  1200 |
   +----+----------+-------+
   2 rows in set (0.01 sec)
   ```

   

