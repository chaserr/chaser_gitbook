1. 使用 pm2 运行 sails框架 生产环境的 node 项目
```js
pm2 restart app.js -- --prod
```

2.  从服务器下载文件
scp -i /Users/tongxing/wedding/weddings.pem ec2-user@ec2-13-115-179-184.ap-northeast-1.compute.amazonaws.com:/home/ec2-user/server.js /Users/tongxing/wedding

> `/Users/tongxing/wedding/weddings.pem`：  公钥地址
> `ec2-user@ec2-13-115-179-184.ap-northeast-1.compute.amazonaws.com`： ec2-user@ + 公有 DNS (IPv4)
> `/home/ec2-user/server.js ` 服务器上文件路径
> `/Users/tongxing/wedding` 本地要存储的文件目录

3. 从本地上传文件夹
