#利用 AWS 的 EC2来搭建属于自己的 VPN 服务器（MAC平台）
其实网上有不少的教程了，但是还是有很多坑，我是一个追求完美的人，因为最近在利用 AWS 的 EC2来部署自己的 node项目，所以顺手搭建了一个 vpn。
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






