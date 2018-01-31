###1. 在网上总是看到 React.createClass，但是官方总是 extends Component

那么React.createClass和extends Component的区别到底是什么

> createClass本质上是一个工厂函数，extends的方式更加接近最新的ES6规范的class写法。两种方式在语法上的差别主要体现在方法的定义和静态属性的声明上。createClass方式的方法定义使用逗号,隔开，因为creatClass本质上是一个函数，传递给它的是一个Object；而class的方式定义方法时务必谨记不要使用逗号隔开，这是ES6 class的语法规范。

React.createClass和extends Component的区别主要在于：

1. 语法区别

2. propType 和 getDefaultProps

3. 状态的区别

4. this区别

5. Mixins

答案：
https://segmentfault.com/a/1190000005863630

###2. props与state
 1. **props属性：组件可以定义初始值，自己不可更改props属性值，只允许从父组件中传递过来**

```js
// 父组件
class MainComponent extends React.Component{
    render(){
        return(<Label name="标题栏">);
    }
}

// 子组件
class Label extends React.Component{
    render(){
        return(<Text>{this.props.name}</Text>);
    }
}
```

2. **state属性：组件用来改变自己状态的属性，通常使用setState({key:value})来改变属性值，不能使用this.state.xxx来直接改变（类似于 OC 中的 get 方法），setState({key:value})方法会触发界面刷新。**
> setState({key:value})类似于 OC 中的 set 方法，this.state.xxx类似于 OC 中的 get 方法



3. 获取某个控件实例，通过添加 `ref:xxx`标记，然后再通过`this.refs.xxx`获取

4. 如果某个方法中使用到了 this,那么这个方法一定要绑定 this，否则会报错：**`reactnative 'this.xxx' is undefined)`**

```js
//es5 在constructor(构造函数)方法里绑定
this.xxx = this.xxx.bind(this);
//使用ES6新特性箭头函数=>()

```