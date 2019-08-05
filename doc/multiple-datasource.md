

## spring boot 配置多数据源

```java
#数据源配置
spring:
  datasource:
    dynamic:
      primary: lims
      datasource:
        busy1:
          driver-class-name: org.postgresql.Driver
          url: jdbc:postgresql://localhost:5432/busy1
          username: postgres
          password: 123456
        busy2:
          driver-class-name: org.postgresql.Driver
          url: jdbc:postgresql://192.168.0.151:5432/busy2
          username: postgres
          password: 123456
```

```
 attributes.setProperty("get*", "PROPAGATION_REQUIRED,readOnly");
 ...
 return new TransactionInterceptor(transactionManager, attributes);
```

“只读事务”并不是一个强制选项，它只是一个“暗示”，提示数据库驱动程序和数据库系统，这个事务并不包含更改数据的操作，那么JDBC驱动程序和数据库就有可能根据这种情况对该事务进行一些特定的优化，比方说不安排相应的数据库锁，以减轻事务对数据库的压力，毕竟事务也是要消耗数据库的资源的。
