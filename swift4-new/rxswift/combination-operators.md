# Combination Operators

Combination Operators实现将多个Observables组合成一个Observable

Combination Operators在RxSwift中的实现有五种：

* startWith
* merge
* zip
* combineLatest
* switchLatest

## startWith

在Observable释放元素之前，发射指定的元素序列。[更多详情](http://reactivex.io/documentation/operators/startwith.html) 就是在发送一个东西之前，我先发送一个我指定的东西。

```swift
Observable.of(["C", "C++","OC"]).startWith(["先吃饱饭在学习"]).subscribe { (event) in
            print(event)
        }.disposed(by: disposeBag)
```

在我们发射\["C","C++","OC"\]之前，我们指定了发射\["先吃饱饭再学习"\]，因此控制台上的打印结果是：

```swift
next(["先吃饱饭在学习"])
next(["C", "C++", "OC"])
completed
```

startWith和栈类似，先进后出。什么意思呢？你可以在上面的例子中再添加几个.startWith\(\)试试。

## merge

将多个Observable组合成单个Observable,并且按照时间顺序发射对应事件。[更多详情](http://reactivex.io/documentation/operators/merge.html)

```swift
let subject1 = PublishSubject<String>()
        let subject2 = PublishSubject<String>()

        Observable.of(subject1,subject2).merge().subscribe(onNext: {
            print($0)
        }).disposed(by: disposeBag)

        subject1.onNext("A")
        subject1.onNext("B")
        subject2.onNext("①")
        subject2.onNext("②")
        subject1.onNext("AB")
        subject2.onNext("③")
```

## zip

将多个Observable\(注意：必须是要成对\)组合成单个Observable，当有事件到达时，会在每个序列中对应的索引上对应的元素发出。\(ps:之前版本的RxSwift好像最多只能组合8个Observable\)[更多详情](http://reactivex.io/documentation/operators/zip.html)

```swift
let subject1 = PublishSubject<String>()
        let subject2 = PublishSubject<String>()

        Observable.zip(subject1, subject2) { string1, string2 in
            "\(string1) \(string2)"
        }.subscribe(onNext: {
            print($0)
        }).disposed(by: disposeBag)

        subject1.onNext("A")
        subject1.onNext("B")
        subject1.onNext("C")
        subject2.onNext("1")
        subject2.onNext("2")
```

上述代码，因为subject.onNext\("C"\)没有与之对应的Observable，因此不会打印，让我们看看打印结果：

```swift
A 1
B 2
```

## combineLatest

当一个项目由两个Observables发射时，通过一个指定的功能将每个Observable观察到的最新项目组合起来，并根据该功能的结果发射事件。[更多详情](http://reactivex.io/documentation/operators/combinelatest.html)

```swift
let subject1 = PublishSubject<String>()
let subject2 = PublishSubject<String>()

Observable.combineLatest(subject1, subject2) { (string1, string2) in
       "\(string1) -- \(string2)"
  }.subscribe(onNext: {
    print($0)
  }, onError: nil, onCompleted: nil, onDisposed: nil).disposed(by: disposeBag)

  subject1.onNext("A")
  subject2.onNext("1")
  subject2.onNext("2")
  subject1.onNext("B")
  subject1.onNext("C")
```

输出结果

```swift
A--1
A--2
B--2
C--2
```

图解 ![](https://images2015.cnblogs.com/blog/1202156/201707/1202156-20170721153506214-411196330.png)

## switchLatest

切换Observable队列。

```swift
let subject1 = BehaviorSubject (value: "1")
        let subject2 = BehaviorSubject (value: "A")

        let variable = Variable.init(subject1)

        variable.asObservable().switchLatest().subscribe(onNext: {
            print($0)
        }).disposed(by: disposeBag)

        subject1.onNext("2")
        subject1.onNext("3")

        variable.value = subject2

        subject1.onNext("4")
        subject2.onNext("B")
```

打印结果：

```swift
1
2
3
A
B
```

![](https://images2015.cnblogs.com/blog/1202156/201707/1202156-20170721153533027-685359602.png)

