# mysql创建一个新用户，登陆失败：`Access denied for user 'auser'@'localhost' (using password: YES)`

今天碰到一个奇怪的问题，创建了一个新用户，用该用户登陆的时候居然登陆失败：

```mysql
mysql> CREATE USER 'auser'@'%' IDENTIFIED BY 'passworld';
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT ALL PRIVILEGES ON db . * TO 'auser'@'%';
Query OK, 0 rows affected (0.00 sec)

mysql> exit
Bye
[root@VM_16_9_centos ~]# mysql -uauser -p
Enter password: 
ERROR 1045 (28000): Access denied for user 'auser'@'localhost' (using password: YES)
```

后来，是这样解决的：`delete from user where User='';` 之后再登陆就能登陆成功了。

```mysql
mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> select User,Host from user;
+--------+-------------------+
| User   | Host              |
+--------+-------------------+
| auser  | %                 |
| root   | 127.0.0.1         |
| root   | ::1               |
|        | localhost         |
| root   | localhost         |
|        | vm\_16\_9\_centos |
| root   | vm\_16\_9\_centos |
+--------+-------------------+
mysql> delete from user where User='';
Query OK, 2 rows affected (0.00 sec)

mysql> select User,Host from user;
+--------+-------------------+
| User   | Host              |
+--------+-------------------+
| auser  | %                 |
| root   | 127.0.0.1         |
| root   | ::1               |
| root   | localhost         |
| root   | vm\_16\_9\_centos |
+--------+-------------------+
5 rows in set (0.00 sec)


[root@VM_16_9_centos ~]# mysql -uauser -p
Enter password: 

```

