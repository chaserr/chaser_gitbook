1. 大小端问题

假如公司后台是java开发的，而之间又要进行数据传输，那么可能就会涉及大小端的数据问题。因为java默认是大端，iOS采用的是小端模式(**固件也是属于小端**)，所以需要转化。具体操作如下：
```c
int checkCPUendian() {//返回1，为小端；反之，为大端；
    union
    {
        unsigned int  a;
        unsigned char b;
    }c;
    c.a = 1;
    return 1 == c.b;
}
```

再将小端转化为大端, 利用C语言函数ntohl()进行转换
```c
int newSize = size;
    if (checkCPUendian() == 1) {
        newSize = ntohl(size);//小端转大端
    }
```