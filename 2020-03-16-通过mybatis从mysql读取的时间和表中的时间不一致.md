# 2020-03-16-通过mybatis从mysql读取的时间和表中的时间不一致

近日在开发一个SpringBoot项目，利用Mybatis来进行Dao的操作。在页面上展示数据的时候发现，时间和数据库表中存的时间不一致。

经过查询资料，通过两步解决了该问题：

## 一、修改mysql的时区(保留，不一定需要)

参考：[《mybatis查询mysql的datetime类型数据时间差了14小时（时区问题）》](https://blog.csdn.net/yjgithub/article/details/80404002)

```mysql
mysql> show variables like '%time_zone%';
+------------------+--------+
| Variable_name    | Value  |
+------------------+--------+
| system_time_zone | CST    |
| time_zone        | SYSTEM |
+------------------+--------+
mysql> set global time_zone= '+8:00';
Query OK, 0 rows affected (0.00 sec)

mysql> set  time_zone= '+8:00';
Query OK, 0 rows affected (0.00 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.04 sec)

mysql> show variables like '%time_zone%';
+------------------+--------+
| Variable_name    | Value  |
+------------------+--------+
| system_time_zone | CST    |
| time_zone        | +08:00 |
+------------------+--------+
2 rows in set (0.00 sec)
```

## 二、修改mybatis中映射的jdbcType

mysql的字段类型为`datetime`，项目中Bean的字段类型为`Instant`，映射是jdbcType类型应该为：TIMESTAMP

如：

```
...
@Result(column = "issue_time", property = "issueTime", jdbcType = JdbcType.TIMESTAMP),
...
```



