# this

this，在一个类中的不同地方，比如闭包函数中，this的指向并不都是这个类的实例。（经常出问题的地方，需不需要.bind\(this\)） 普通函数.bind\(this\) 来把内部函数中的 this 绑定到了外部函数去。 箭头函数里的this还是原来的this，不需要额外绑定。

