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
> EC2默认防火墙是阻断所有流量，因此在EC2的Scurity Groups(安全组)中除了其他必要的规则之外，还要添加一条入口流量规则：TCP的3306端口的任意源IP的连接都允许。