# 应用重签名

1. 首先在 pp 助手下载一个越狱应用 .ipa

   > 下载的时候手机不能连接电脑，否则无法下载

2. 查看是否真的砸壳了

   cryptid == 0 则代表以及将 ipa 解密\(砸壳\)了

```ruby
# `grep -B 2 crypt` linux命令
# grep是一个搜索程序,它只能搜索匹配一个正则表达式的一行的存在性
#  找出crypt中带有keyword的行，输出中除显示该行外，还显示之前的2行(Before 2)。其中，数字可以变。
# crypt加密方法


➜  Desktop otool -l /Users/tongxing/Desktop/yueyuTest/kuwo/Payload/KWPlayer.app/KWPlayer  | grep -B 2 crypt
          cmd LC_ENCRYPTION_INFO
      cmdsize 20
     cryptoff 16384
    cryptsize 15876096
      cryptid 0
--
          cmd LC_ENCRYPTION_INFO_64
      cmdsize 24
     cryptoff 16384
    cryptsize 18055168
      cryptid 0
```

> cmd 有几个就代表有几个架构模式 查看架构命令：
>
> ```ruby
> # file <二进制文件路径>
> file /Users/tongxing/Desktop/yueyuTest/kuwo/Payload/KWPlayer.app/KWPlayer
> /Users/tongxing/Desktop/yueyuTest/kuwo/Payload/KWPlayer.app/KWPlayer: Mach-O universal binary with 2 architectures: [arm_v7:Mach-O executable arm_v7] [arm64]
> /Users/tongxing/Desktop/yueyuTest/kuwo/Payload/KWPlayer.app/KWPlayer (for architecture armv7):    Mach-O executable arm_v7
> /Users/tongxing/Desktop/yueyuTest/kuwo/Payload/KWPlayer.app/KWPlayer (for architecture arm64):    Mach-O 64-bit executable arm64
> ```

1. 拷贝yololib，需要替换的embedded.mobileprovision和需要注入的.dylib 到 xxx.app文件路径下
2. 使用.dylib命令注入

```ruby
./yololib /Users/tongxing/Desktop/yueyuTest/kuwo/Payload/KWPlayer.app/KWPlayer  RevealServer.dylib
2018-01-05 14:50:15.353 yololib[51957:1916283] dylib path @executable_path/RevealServer.dylib
2018-01-05 14:50:15.354 yololib[51957:1916283] dylib path @executable_path/RevealServer.dylib
Reading binary: /Users/tongxing/Desktop/yueyuTest/kuwo/Payload/KWPlayer.app/KWPlayer

2018-01-05 14:50:15.355 yololib[51957:1916283] FAT binary!
2018-01-05 14:50:15.355 yololib[51957:1916283] Injecting to arch 9
2018-01-05 14:50:15.355 yololib[51957:1916283] Patching mach_header..
2018-01-05 14:50:15.355 yololib[51957:1916283] Attaching dylib..

2018-01-05 14:50:15.355 yololib[51957:1916283] Injecting to arch 0
2018-01-05 14:50:15.355 yololib[51957:1916283] 64bit arch wow
2018-01-05 14:50:15.357 yololib[51957:1916283] dylib size wow 64
2018-01-05 14:50:15.357 yololib[51957:1916283] mach.ncmds 53
2018-01-05 14:50:15.357 yololib[51957:1916283] mach.ncmds 54
2018-01-05 14:50:15.357 yololib[51957:1916283] Patching mach_header..
2018-01-05 14:50:15.358 yololib[51957:1916283] Attaching dylib..

2018-01-05 14:50:15.359 yololib[51957:1916283] size 59
2018-01-05 14:50:15.359 yololib[51957:1916283] complete!
```

> 出现complete dai'biao 注入成功，可以使用 mach-O-View来查看

1. 重签名
2. PP助手安装

