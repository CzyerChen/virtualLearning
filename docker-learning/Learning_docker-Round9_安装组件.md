#### 安装redis

#### 安装MySQL
- 正常运行mysql，主要注意挂载数据目录、配置目录、root的密码、其他内置用户和目录，端口、用户、名称
```text
docker run -d -p 3306:3306 -v /xxxx/docker/mysql/conf:/etc/mysql/mysql.conf.d -v /xxxx/docker/mysql/data:/var/lib/mysql  -e MYSQL_ROOT_PASSWORD=xxxx --name mysql mysql:5.7
```
- 运行应该是没有问题，但是往往事情没有这么顺利，进入容器内部登录或者在外部用安装的mysql-client登录都可以
```text
内部：mysql -uroot -p
外部：mysql -h127.0.0.1 -P3306 -uroot -p
```
- 以为这样就可以解决问题了，然后太天真，报错 ERROR 1045 (28000): Access denied for user 'mysql'@'localhost' (using password: YES)
- 当然首先确保你的用户名密码没错，那么开始以下步骤（亲测）：
- 步骤1
```text
在/data/mysql/conf目录下面新建my.cnf文件，如果挂载到外部，在外部修改也是可以的，反正需要重启容器

加入
[mysqld]
skip-grant-tables

意思不进行权限的认证
```
- 步骤2：重启服务:docker restart mysql
- 步骤3：mysql -h -u -p登录很顺利，因为没有校验
- 步骤4：修改密码
```text
//先把密码置空，root用户无需密码
select user,authentication_string from user;
update user set authentication_string=''  where user='root';
```
- 步骤5：注释掉my.cnf 文件中的 skip-grant-tables
- 步骤6：重启MySQL
- 步骤7：重置密码，并刷新权限
```text
alter user'root'@'%' IDENTIFIED BY '123456'; 
alter user'root'@'localhost' IDENTIFIED BY '123456'; 

flush privileges;
```
- 步骤8：如果是mysql8.0以上，还有一个步骤
MYSQL 8.0内新增加mysql_native_password函数，通过更改这个函数密码来进行远程连接。更改ROOT用户的native_password密码
```text
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY'Mysql@123';
```


