# LLDB调试技巧

1. bt--thread backtrack

该命令会调用方法堆栈信息

1. frame variable

打印出当前frame的所有变量， 如果需要打印指定变量使用`frame variable self -> 变量名`

1. image 

   命令可用于寻址，有多个组合命令。比较实用的用法是用于寻找栈地址对应的代码位置。 下面我写了一段代码

```text
image lookup --address 0x0000000100004af8
```

