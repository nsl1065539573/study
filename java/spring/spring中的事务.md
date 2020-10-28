spring的事务是基于AOP实现的，而AOP是以方法为单位的，spring的事务属性分为传播行为、隔离级别、只读和超时属性。

#### spring事务的传播行为

| 属性名称                  | 值            | 描 述                                                        |
| ------------------------- | ------------- | ------------------------------------------------------------ |
| PROPAGATION_REQUIRED      | required      | 如果当前没有事务，则新建一个事务，如果已经在一个事务中，则加入这个事务 |
| PROPAGATION_SUPPORTS      | supports      | 支持当前事务。如果当前没有事务，就以非事务形式运行           |
| PROPAGATION_MANDATORY     | mandatory     | 支持当前事务。使用当前的事务，如果当前没有事务，就抛出异常   |
| PROPAGATION_REQUIRES_NEW  | requires_new  | 将创建新的事务，如果当前有事务，则把当前事务挂起             |
| PROPAGATION_NOT_SUPPORTED | not_supported | 以非事务形式运行，如果当前已经存在事务，则将当前事务挂起     |
| PROPAGATION_NEVER         | never         | 以非事务形式运行，如果当前已经存在事务，则抛出异常           |
| PROPAGATION.NESTED        | nested        | 如果当前存在事务，则使用嵌套事务执行，如果当前没有事务，则执行与PROPAGATION_REQUIRED类似的操作 |