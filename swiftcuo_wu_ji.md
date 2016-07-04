# swift错误集

如果我们遇到了多重 Optional 的麻烦的时候，这显然对我们是没有太大帮助的。我们可以使用 fr v -R 命令来打印出变量的未加工过时的信息，就像这样：

``` objc
(lldb) fr v -R anotherNil
(Swift.Optional<Swift.Optional<Swift.String>>)
    anotherNil = Some {
    ... 中略
}
(lldb) fr v -R literalNil
(Swift.Optional<Swift.Optional<Swift.String>>)
    literalNil = None {
    ... 中略
}
```