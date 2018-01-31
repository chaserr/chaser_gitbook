
我们把组件从装载,到渲染，再到卸载当做一次生命周期，也就是组件的生存状态从装载开始到卸载为止，期间可以根据属性的变化进行多次渲染。

生命周期的三种状态：

Mounting：装载，
Updating：渲染
Unmounting：卸载

`componentWillMount()`，组件开始装载之前调用，在一次生命周期中只会执行一次。
`componentDidMount()`，组件完成装载之后调用，在一次生命周期中只会执行一次，从这里开始就可以对组件进行各种操作了，比如在组件装载完成后要显示的时候执行动画。
`componentWillUpdate(object nextProps, object nextState)`，组件属性更新之前调用，每一次属性更新都会调用
`componentDidUpdate(object prevProps, object prevState)`，组件属性更新之后调用，每次属性更新都会调用
`componentWillUnmount()`，组件卸载之前调用