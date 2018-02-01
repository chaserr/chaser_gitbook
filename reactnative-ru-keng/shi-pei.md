为了提高代码的兼容性，我们有时需要判断当前系统的平台，然后做一些适配。比如，我们在使用StatusBar做导航栏的时候，在iOS平台下根视图的位置默认情况下是占据状态栏的位置的，我们通常希望状态栏下面能显示一个导航栏，所以我们需要为StatusBar的外部容器设置一个高度：

```js
//这三个点其实就是对象的扩展运算符，说白了就是把panHandlers对象里面所有的属性填充到View中
<View style={{height: Platform.OS === 'ios' ? 20:0}}>
    <StatusBar {...this.props.statusBar} />
</View>;
```