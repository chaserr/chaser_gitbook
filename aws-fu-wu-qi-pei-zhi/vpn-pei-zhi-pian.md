#利用 AWS 的 EC2来搭建属于自己的 VPN 服务器（MAC平台）
其实网上有不少的教程了，但是我觉得没几篇是真的自己一步一步来的，即使有可能自己摸索出来了，遇到的坑也没说出来，其实还是有很多坑，我是一个追求完美的人。因为最近在利用 AWS 的 EC2来部署自己的 node项目，所以顺手搭建了一个 vpn。
这里假定你已经拥有了 AWS 的账户，没有的先去申请一个，AWS 的服务首年免费，次年如果不想付费可以继续申请一个新的账号
>注意在申请新的账号的时候不要勾选额外的服务，否则你会被无端扣费，我之前就被扣了几百美元，不过国外平台就是服务不错，如果你真没有使用过这些服务，可以申诉回来的，找亚马逊客服，我的五百大洋就找回来了。

-------
亚马逊的网站也支持中文简体，英文不好的童靴可以选择中文简体，页面左下方。
![语言切换](https://thumbnail0.baidupcs.com/thumbnail/91e8bec5addba1c8ab4c450e919c288b?fid=3072304475-250528-796673921897664&time=1513213200&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-Kf2E%2BtFfDZqIpYTXQAZqOF7AEJI%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=8057320134605788635&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

>EC2实例就相当于一个轻小型的服务器

##1.选择区域
登录到 AWS 首页后，进入 AWS 服务，先选择右上角的地区，这里选择的地区说简单点就是你搭建的这个服务器所在的区域，我们选择亚太区域东京，可以检测各个区域的网络节点延迟，东京延迟是最低的
![选择区域](https://thumbnail0.baidupcs.com/thumbnail/5f18cf131c383e5fe8fd4f18e841308d?fid=3072304475-250528-110440392664857&time=1513213200&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-ES1McoiVP8AiyIn840wwCbTU4NY%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=8057459706034865069&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

##2. 建立 EC2实例
1. 选中所有服务->计算->EC2
![](https://thumbnail0.baidupcs.com/thumbnail/321e9021eefd09b205262843b08c2fa0?fid=3072304475-250528-215634320790342&time=1513213200&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-2lEJr1l86kqvsaJQQLLdWOz9MhE%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=8057495766631509329&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

2. 点击启动实例
这里不需要管其他东西
![](https://thumbnail0.baidupcs.com/thumbnail/1bdac83243e43826deb52d0d143968ae?fid=3072304475-250528-416883028431825&time=1513213200&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-bbYwRSTy%2Fy737hQc0o%2BhPsqeS6E%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=8057525621995321238&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

>这个截图中可以看到我的账户有两个正在运行的实例，分别绑定了两个 ip 地址，安全组以及秘钥对。后面会介绍


3. 选择虚拟机，也就是要装在你的服务器的操作系统
勾选仅免费套餐，选中 Ubuntu Server, 点击选择进入下一步
![](https://thumbnail0.baidupcs.com/thumbnail/c49781ded4f039f9f17adde2e4524178?fid=3072304475-250528-396178872292859&time=1513216800&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-LLkuocop4BbK4gdfpwlZLbApd%2B0%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=8057583244936900717&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

4. 选择实例类型
这里是默认的，因为只有这个是免费的，所以不需要改动，直接点击审核和启动
![](https://thumbnail0.baidupcs.com/thumbnail/a9e76937afa62fa93036d5c7670f0b51?fid=3072304475-250528-338276623328411&time=1513216800&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-rndk%2B1%2BUXViylmiDfAlOOYUAsww%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=8057728614766684051&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

5. 核查实例启动
然后就到了最后一步，中间的几部不需要管，有兴趣可以点进去看看，但是不需要做任何更改。点击启动
![](https://thumbnail0.baidupcs.com/thumbnail/fc2ed1fcc8dd34d26e4d0847484c56cc?fid=3072304475-250528-1089840389609117&time=1513216800&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-o%2B5AiRepLMLQntGdBUn4jOAzuaQ%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=8057783629294564498&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

6. 创建秘钥
这里需要创建一对秘钥，秘钥是用来远程登录服务器的，也就是接下来会在你自己的电脑上登录 AWS 的 EC2服务器，并在里面配置项目。


- 点击创建新秘钥对
- 填写秘钥名称，可以填 TXVPN
- 下载秘钥对
> 只有下载了秘钥对才能启动实例，一定要保存好这个秘钥对，很重要
- 启动实例
![](https://thumbnail0.baidupcs.com/thumbnail/1d351b84c9cd563f93a11cacf3087283?fid=3072304475-250528-902762744644995&time=1513216800&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-d2NB91M5gip1JPfhQCaOxU1FsVM%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=8057888254682721670&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

接下来就可以看到实例已经启动了，这时候可以为实例设置一个名字方便好记
![](https://thumbnail0.baidupcs.com/thumbnail/98cd52450e26b7d51dc8c63c4abfaa38?fid=3072304475-250528-897797000926147&time=1513216800&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-xw8U6IU58GSB8hLGZL69YRHri1s%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=8057953274911716318&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

7. 创建安全组，这个安全组可以指定访问的 ip，端口等等
点击网络与安全->安全组
会看到组名，刚才创建的实例默认是会创建一个安全组的，可以在实例界面拖到最后面查看默认生成的安全组名，然后在这个界面选中与刚才创建的实例相关联的安全组


- 点击入站
- 编辑

8. 编辑入站规则
- 添加规则
> 选择自定义 TCP 规则，在端口范围中填写1723，任何位置

- 保存
![](https://thumbnail0.baidupcs.com/thumbnail/4955cbb430bc1116a12f82144a02cb63?fid=3072304475-250528-973661698326341&time=1513216800&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-Lja%2Fc0qIDPGKsc9sTsPgmNjeF60%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=8058093325108299947&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

9. 添加弹性 IP
点击网络与安全->弹性 ip->分配新地址
不添加了弹性ip，你的实例每次重新启动就会改变ip
![](https://thumbnail0.baidupcs.com/thumbnail/3e13e0d0f3a73e598b9fff4076106fdf?fid=3072304475-250528-779082230089233&time=1513216800&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-0CaETRlFhtPaqSazgsR9EBB4C8s%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=8058168781069338456&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

10.关联弹性 ip
分配完之后 会回到分配新地址界面，这个时候选中已经分配的 ip
点击操作-> 关联地址
![](https://thumbnail0.baidupcs.com/thumbnail/c5e51a4eb913a766d387ab8e963d9e5e?fid=3072304475-250528-712032148358442&time=1513216800&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-qWqtoE2u4T5u9qEO%2Fdpt2luwYuY%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=8058228392251079873&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)


- 选择实例
> 这里会列出所有你创建的实例，所以前面的实例别名很重要，选中别名 TXVPN
- 重新关联
- 关联
![](https://thumbnail0.baidupcs.com/thumbnail/ae959a273035fd92e7abe8e52f8f706d?fid=3072304475-250528-77181790877379&time=1513216800&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-AJzShtg27GYcD8QhaELArNQEN8g%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=8058303092413090187&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)



##3. 远程登录服务器，远程连接
点击实例->选择创建的 TXVPN 实例->连接
![](https://thumbnail0.baidupcs.com/thumbnail/7141139b6c8578a1a908293fd8178423?fid=3072304475-250528-569474135399144&time=1513216800&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-DrGTq33vR8CUTxeB0prE0NegrXQ%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=8058363307618283443&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

打开 mac 的终端或者 iTerm
cd 到你之前下载秘钥的路径
复制点击关联弹出来的弹框上面的两个命令行，如果是一步一步下来的，不需要做任何更改。
![](https://thumbnail0.baidupcs.com/thumbnail/5b9faa28017bd28bebc3b2a7d495f617?fid=3072304475-250528-415918390437801&time=1513216800&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-PWxcT8I%2B5F%2F9Q1xumkP8m0vhPBQ%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=8058453137426015114&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

第一次连接会出现一个提示，带有（yes/no）字样的，这里输入yes回车即可

如果出现
```ruby
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.4.0-1041-aws x86_64)
```
说明已经等了到远程服务器了，只是这个服务器上什么也没有
![](https://thumbnail0.baidupcs.com/thumbnail/b9d8138328b2b7ce1d86174d4000aa06?fid=3072304475-250528-30415768496528&time=1513220400&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-kdsnEFBQ1n%2FKIGUFVZwPjRImqdA%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=8058552264900329965&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

##4. 搭建 VPN 服务器
这里的步骤也是参考网络的文章，但是会备注几点坑
vim 的操作命令也要熟悉一点，至少知道增删改查
###4.1. 配置 pptp
1. 在刚才登录的终端输入
```ruby
sudo apt-get install pptpd
```
2. 出现确认后输入 y回车
3. 配置 pptp
在终端输入
```ruby
sudo vi /etc/pptpd.conf
```
找到文件最后面，去掉下面两行的注释符号#，没有的在最后面加上这两行
localip 192.168.0.1
remoteip 192.168.0.234-238,192.168.0.245
4. 按键盘上的ESC键，左下角的– INSERT —字样消失
5. SHIFT键的同时按一下;键，光标跳转到左下角的:号后面，输入 wq 回车
###4.2. 添加 DNS 解析
1. 在终端输入
```ruby
sudo vi /etc/ppp/pptpd-options
```
找到带有ms-dns的两行，并修改 dns 为下面谷歌开放的，没有的可以在最后面加上下面两行
ms-dns 8.8.4.4
ms-dns 8.8.8.8
2. 保存并退出 vim（命令操作同上4-5）步
###4.3. 添加 VPN 用户
在终端输入
```ruby
sudo vim /etc/ppp/chap-secrets
```
> 坑1，一定要注意这里的格式 与字段要一一对应

| client | server | secret | IP addresses |
| - | :-: | -: | :-:|
| TXVPN | pptpd| ****** | * | 

其中，用户名(client)和密码(secret)可自行设置，服务名必须是pptpd，IP表明允许登录的ip列表，如果允许所有ip可以设置为*。
如果需要给朋友也加以账号，可以在这里填写，注意的是 AWS 免费的每个月都有流量限制的，慎用哦
###4.4. 开启IPv4转发
1. 在终端输入
```ruby
sudo vim /etc/sysctl.conf
```
2. 找到
```ruby
#net.ipv4.ip_forward = 1
```
并将注释符号#去掉
3. 退出 vim 编辑器

###4.4. 创建NAT规则

1. 在终端输入
```ruby
sudo vim /etc/rc.local
```
2. 在 exit 0 这一行的上面添加一行
```ruby
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE  # 将所有目标IP包转向eth0接口
```
3. 退出 vim 编辑器

pptpd默认监听1723端口，所以需要开启相关端口
```ruby
sudo iptables -I INPUT -p tcp --dport 1723 -j ACCEPT
```
> 是--dport哦

###4.5. 更新配置
在终端输入
```ruby
sysctl -p
```
###4.6. 重启 pptpd 服务
在终端输入并回车
```ruby
sudo /etc/init.d/pptpd restart
```
会出现 ok  restarting pptpd：pptpd.servoce
在终端输入并回车
```ruby
sudo netstat -ntpl
```
如果出现
```ruby
tcp        0      0 0.0.0.0:1723            0.0.0.0:*               LISTEN      11779/pptpd
```
说明访问正常

##连接 VPN
在mac 和 iphone 上现在都已经取消了 pptpd 服务，但是可以通过第三方来连接，这里推荐一个软件
`shimo`这个软件有破解版的,附赠下载地址：
[shimo 破解版下载地址](https://www.waitsun.com/shimo-4-1-5-1.html)
打开 shimo