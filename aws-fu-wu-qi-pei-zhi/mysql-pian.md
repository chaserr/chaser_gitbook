1. /var/lib/mysql ，配置文件路径为/etc/my.cnf

2. 设置用户名和密码
```ruby
/usr/bin/mysqladmin -u root password 'root'
```

3. 进入数据库
```ruby
mysql -u root -proot
```

4. 授权远程登录账户和密码
```ruby
>mysql grant all on *.* to root@'%' IDENTIFIED BY 'root' with grant option;
>mysql flush privileges #刷新mysql权限

```