###1. State
说到state就不得不提React Native的UI更新机制。与Android和iOS原生开发不同，原生开发的UI更新需要显示调用方法。以Android为例，在数据发生变化后，需要在代码中调用text.setText("新数据")去更新Text，或者adapter.notifyDataSetChanged()去更新列表。

而在RN中，如果组件使用state作为数据来源，由于系统会持续监控state，当state中的任何属性被改变时，组件都会被重绘，并触发render方法重绘UI，而不需要显示调用更新方法。我们可以在数据变化之后调用this.setState来更新界面，而不是this.state={data:'new'},等号直接赋值符合编程语法，但是不会重绘界面。注意，setState这个方法不是即时生效的，如果你的业务逻辑需要立即判断是否变化的话，请使用类的成员变量。

需要使用state的组件主要有：处理用户输入、网络数据获取、定时更新、消息推送等不可预知状态改变的组件。

在说一下state的使用规则：

1、由于state任何属性的改变都会导致UI重绘，而UI重绘会消耗系统资源，所以在封装可复用的组件时，尽量不用或少用state，而是通过props将数据传递到组件内部（props在组件内部是不可变的，不会导致UI重绘）。

2、在state内部，如果一个变量可以通过state或者props中的另一个变量计算得出的话，删除这个变量，等到需要使用的时候再通过计算得出。

3、让UI中的可变数据仅仅来源于state。

4、如果想要给组件添加子组件，千万不要把子组件放到state中，而是直接写在render方法中。

###2. Props
也就是属性，主要用于存放组件的相关信息，比如长宽高颜色等等。在父组件中赋值之后，子组件直接取值使用。站在子组件的角度来看，父组件赋给的属性值是不变的。

可以通过声明defaultProps指定默认值，声明propTypes可以控制属性类型。在声明propTypes时，在后面加上isRequireed表示此属性为必须属性。propTypes仅仅在开发环境中有效，正式发布的app是不会进行检查的，不会编译propTypes代码。属性默认值和类型是非必要的。

###3.成员变量

一般存放于逻辑控制相关的各种Tag，和各种本地配置。