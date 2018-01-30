1. 在网上总是看到 React.createClass，但是官方总是 extends Component

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