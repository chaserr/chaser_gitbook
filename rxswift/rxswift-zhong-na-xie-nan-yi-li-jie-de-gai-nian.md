#概念
- Observable 和 Observer
- subscribe 和 subscribe(onNext:)
- Dispose 和 DisposeBag
- observeOn() 和 subscribeOn()
- shareReplay
- 自定义operator
- Driver
- map 和 flatMap 何时使用
- UIBindingObserver
- doOn

>Rx系列的核心就是Observable Sequence

####Observable 和 Observer
理解：
比如一个宝宝在睡觉，爸爸妈妈不可能时时刻刻待在那看着吧？那样子太累
了。他们该做啥做啥，只要听到宝宝哭声的时候，他们给宝宝喂奶就行了。这就是一个简单的观察者模式。宝宝是被观察者，爸爸妈妈是观察者也称作订阅者，只要被观察者发出了某一个事件，比如宝宝哭声，叫声都是一个事件，订阅者就会做出相应地响应。

理解了观察者模式这两个概念就很好理解了，Observable就是可被观察的，也就是我们说的宝宝，他也是事件源。而Observer就是我们的观察者，也就是当收到事件的时候去做某些处理的爸爸妈妈。观察者需要去订阅(subscribe)被观察者，才能收到Observable的事件通知消息。

####subscribe 和 subscribe(onNext:)

subscribe是订阅sequence发出的事件，比如next事件，error事件等。而subscribe(onNext:)是监听sequence发出的next事件中的element进行处理，他会忽略error和completed事件。相对应的还有subscribe(onError:) 和 subscribe(onCompleted:)。

####Dispose 和 DisposeBag

当监听一个sequence的时候，有消息事件来了，我们做某些事情。但是这个sequence不再发送消息事件了，那么我们的监听也就没有什么存在的价值了，所以我们需要释放我们这些监听资源，其实也就是内存资源释放。
释放某一个监听的时候，我们有两种方式处理：

我们可以手动调用释放方式，但是我们一般不适用这种方式。另一种是一种自动方式

####observeOn() 和 subscribeOn()

这两个东西刚开始看的时候也是一脸懵逼，就知道最好多用observeOn()，但是不知道为什么，下面我们就来揭开它们的面纱看下它们的真面目吧。

区别其实我感觉就一句话，subscribeOn()设置起点在哪个线程，observeOn()设置了后续工作在哪个线程。例如：

```swift
someObservable 
    .doOneThing()  // 1
    .observeOn(MainRouteScheduler.instance) // 2
    .subscribeOn(OtherScheduler.instance) // 3
    .subscribeNext { // 4
        ......
    }
    .addDisposableTo(disposeBag)
```
所有动作都发生在当前的默认线程
observeOn()转换线程到主线程，下面所有的操作都在主线程
subscribeOn()规定动作一开始不是发生在默认线程，而是在OtherScheduler了。
如果我们之前没有调用observeOn()，那么这边会在OtherScheduler发生，但是我们前面调用了observeOn()，所以这个动作会在主线程中调用。

> 总结一下：subscribeOn()只是影响事件链开始默认的线程，而observeOn()规定了下一步动作发生在哪个线程中。

####shareReplay
看官方项目里面的Demo时，我也很疑惑，为什么很多的sequence后面都有shareReplay(1)呢？想的昏头涨脑。
这里我就给大家讲解一下我的理解吧。先看一个例子：
```swift
let disposeBag = DisposeBag()
   
let observable = Observable.just("🤣").map{print($0)}
    
observable.subscribe{print("Even:\($0)")}.disposed(by: disposeBag)
observable.subscribe{print("Even:\($0)")}.disposed(by: disposeBag)
```
打印结果：
```swift
🤣
Even:next(())
Even:completed
🤣
Even:next(())
Even:completed
```
大家发现没有，map()函数执行了两次，但是有些时候，我们并不想让map()函数里面的东西执行两次，比如map()函数里面执行的是网络请求，我只需要执行一次请求，然后把结果提供给大家使用就行了，多余的请求会增加负担。所以这时候就需要使用shareReplay(1)了。这里面的数字一般是1，只执行一次。(ps:我改成 2，3 也只打印一次)

```swift
let disposeBag = DisposeBag()
   
let observable = Observable.just("🤣").map{print($0)}.shareReplay(1)
    
observable.subscribe{print("Even:\($0)")}.disposed(by: disposeBag)
observable.subscribe{print("Even:\($0)")}.disposed(by: disposeBag)
```
结果：
```swift
🤣
Even:next(())
Even:completed
Even:next(())
Even:completed
```
####Driver
这又是啥东东？ 讲解Driver之前我们现在看看下面的🌰：

```swift
let results = query.rx.text
    .throttle(0.3, scheduler: MainScheduler.instance)
    .flatMapLatest { query in
        fetchAutoCompleteItems(query)
    }
    
results
    .map { "\($0.count)" }
    .bindTo(resultCount.rx.text)
    .addDisposableTo(disposeBag)
    
results
    .bindTo(resultsTableView.rx.items(cellIdentifier: "Cell")) { (_, result, cell) in
        cell.textLabel?.text = "\(result)"
    }
    .addDisposableTo(disposeBag)
```

首先创建一个可监听序列results，其中flatMapLatest下面会讲;
然后将results绑定到resultCount.rx.text上;
将results绑定到resultsTableView上.
上面程序会出现下面几个异常：

如果fetchAutoCompleteItems出错，那么它绑定的UI将不再收到任何事件消息；
如果fetchAutoCompleteItems发生在后台线程，那么它绑定的事件也将在后台线程执行，这样更新UI会造成crash；
有两次绑定，fetchAutoCompleteItems就会执行两次
当然针对以上问题，我们也有解决方案，针对第三点，我们可以使用神器shareReplay(1)保证只执行一次，可以使用observeOn()保证后面所有操作在主线程完成。

```swift
let results = query.rx.text
    .throttle(0.3, scheduler: MainScheduler.instance)
    .flatMapLatest { query in
        fetchAutoCompleteItems(query)
            .observeOn(MainScheduler.instance)
            .catchErrorJustReturn([])           
    }
    .shareReplay(1)
                                 
results
    .map { "\($0.count)" }
    .bindTo(resultCount.rx.text)
    .addDisposableTo(disposeBag)
    
results
    .bindTo(resultTableView.rx.items(cellIdentifier: "Cell")) { (_, result, cell) in
        cell.textLabel?.text = "\(result)"
    }
    .addDisposableTo(disposeBag)
```
我们也可以使用Driver来解决：

```swift
let results = query.rx.text.asDriver()
    .throttle(0.3, scheduler: MainScheduler.instance)
    .flatMapLatest { query in
        fetchAutoCompleteItems(query)
            .asDriver(onErrorJustReturn: [])  //当遇见错误需要返回什么
    }   //不需要添加shareReplay(1)
    
results
    .map { "\($0.count)" }
    .drive(resultCount.rx.text)     //和bingTo()功能一样
    .addDisposableTo(disposeBag) 
                                             
results
    .drive(resultTableView.rx.items(cellIdentifier: "Cell")) { (_, result, cell) in
        cell.textLabel?.text = "\(result)"
    }
    .addDisposableTo(disposeBag)
```
drive方法只能在Driver序列中使用，Driver有以下特点：

Driver序列不允许发出error，
Driver序列的监听只会在主线程中。
所以Driver是专为UI绑定量身打造的东西。
以下情况你可以使用Driver替换BindTo:

不能发出error;
在主线程中监听;
共享事件流;

####map 和 flatMap 何时使用

我们来看看map函数和flatMap函数的定义：
map函数，接收一个R类型的序列，返回一个R类型的序列，还是原来的序列。
```swift
public func map<R>(_ transform: @escaping (Self.E) throws -> R) -> RxSwift.Observable<R>

```
flatMap函数，接收一个O类型的序列，返回一个O.E类型的序列，也就是有原来序列里元素组成的新序列。

```swift
public func flatMap<O: ObservableConvertibleType>(_ selector: @escaping (E) throws -> O)
        -> Observable<O.E>
    
```

其实这里的map和flatMap在swift中的作用是一样的。map函数可以对原有序列里面的事件元素进行改造，返回的还是原来的序列。而flatMap对原有序列中的元素进行改造和处理，每一个元素返回一个新的sequence，然后把每一个元素对应的sequence合并为一个新的sequence序列。

####UIBindingObserver
UIBindingObserver这个东西很有用的，创建我们自己的监听者，有时候RxCocoa(RxSwift中对UIKit的一个扩展库)给的扩展不够我们使用，比如一个UITextField有个isEnabled属性，我想把这个isEnabled变为一个observer，我们可以这样做：

```swift
extension Reactive where Base: UITextField {
    var inputEnabled: UIBindingObserver<Base, Result> {
        return UIBindingObserver(UIElement: base) { textFiled, result in
            textFiled.isEnabled = result.isValid
        }
    }
}
```
UIBindingObserver是一个类，他的初始化方法中，有两个参数，第一个参数是一个元素本身，第一个参数是一个闭包，闭包参数是元素本身，还有他的一个属性。

```swift
public init(UIElement: UIElementType, binding: @escaping (UIElementType, Value) -> Swift.Void)

```
自定义了一个inputEnabled关联了UITextField的isEnabled属性。


####doOn
doOn我感觉就是在直接onNext处理时候，先执行某个方法，doOnNext( :)方法就是在subscribe(onNext:)前调用，doOnCompleted(:)就是在subscribe(onCompleted:)前面调用的。

```swift

Observable.of("", "", "", "")
    .do(onNext: { print("Intercepted:", $0) }, onError: { print("Intercepted error:", $0) }, onCompleted: { print("Completed")  })
    .subscribe(onNext: { print($0) },onCompleted: { print("结束") })
    .addDisposableTo(disposeBag)
```
