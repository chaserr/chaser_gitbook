# Filtering and Conditional Operators

RxSwift中包括了：

* filter
* distinctUntilChanged
* elementAt
* single
* take
* takeLast
* takeWhile
* takeUntil
* skip
* skipWhile
* skipWhileWithIndex
* skipUntil

## filter

在Observable序列中只发出满足过滤条件的事件。

```swift
Observable.of(1,2,3,4,5).filter({
            $0 > 2
        }).subscribe(onNext: {
            print($0)
        }).disposed(by: disposeBag)
```

打印结果：

```swift
3
4
5
```

## distinctUntilChanged

过滤连续发出的相同事件

```swift
        Observable.of(1,2,3,3,3,2,2,5,4).distinctUntilChanged().subscribe({print($0)}).disposed(by: disposeBag)
```

结果：

```swift
next(1)
next(2)
next(3)
next(2)
next(5)
next(4)
completed
```

## elementAt

Observable只发出指定位置的事件

```swift
Observable.of(1,2,3,4,5).elementAt(2).subscribe(onNext: {print($0)}).disposed(by: disposeBag)
```

打印结果

```swift
3
completed
```

## single

用法一：检测Observable序列是否发射一个元素。如果发射多个，会报错。

```swift
// error:Sequence contains more than one element
// Observable.of(1,2,3).single().subscribe(onNext: {print($0)}).disposed(by: disposeBag)

Observable.of(1).single().subscribe(onNext: {print($0)}).disposed(by: disposeBag)
```

用法二：检测Observable序列发射的元素里面是否包含多个指定的元素，如果有多个或者没有，会报错。

```swift
Observable.of("🐱", "🐰","🐶","🐸","🐽","🐒")
            .single({
            $0 == "🐸"
        }).subscribe({
            print($0)
        }).disposed(by: disposeBag)

        Observable.of("🐱", "🐰", "🐶", "🐱", "🐰", "🐶")
            .single { $0 == "🐒" }
            .subscribe { print($0) }
            .disposed(by: disposeBag)

        Observable.of("🐱", "🐰", "🐶", "🐶","🐸")
            .single { $0 == "🐶" }
            .subscribe { print($0) }
            .disposed(by: disposeBag)
```

打印结果：

```swift
next(🐸)
completed
error(Sequence doesn't contain any elements.)
next(🐶)
error(Sequence contains more than one element.)
```

## take

从Observable序列开始点起只处理前几个事件。

```swift
Observable.of(1,2,3).take(2).subscribe(onNext: {
            print($0)
        }).disposed(by: disposeBag)
```

打印结果：

```swift
1
2
```

## takeLast

与take相反，从Observable序列的结束位置起，只处理前几个事件。

```swift
        Observable.of("🐱", "🐰", "🐶", "🐶","🐸")
            .takeLast(3)
            .subscribe { print($0) }
            .disposed(by: disposeBag)
```

打印结果

```swift
next(🐶)
next(🐶)
next(🐸)
completed
```

## takeWhile

只处理满足条件的事件。感觉和filter没什么区别

```swift
Observable.of(1,2,3,4,5).takeWhile{$0 < 3}.subscribe(onNext: {print($0)}).disposed(by: disposeBag)
```

打印结果

```swift
1
2
```

## takeUntil

直到另一个Observable序列发出一个信号，则原序列终止。

```swift
let orinSubject = PublishSubject<Int>()
        let refreSubject = PublishSubject<Int>()

        orinSubject.takeUntil(refreSubject).subscribe(onNext: {
            print($0)
        }).disposed(by: disposeBag)

        orinSubject.onNext(1)
        orinSubject.onNext(2)
        refreSubject.onNext(10)
        orinSubject.onNext(3)
```

打印结果

```swift
1
2
```

## skip

从Observable序列的元素跳过指定个数。

```swift
Observable.of(1,2,3,4,5,6).skip(2).subscribe(onNext: {
            print($0)
        }).disposed(by: disposeBag)
```

打印结果：

```swift
3
4
5
6
```

## skipWhile

跳过满足条件的事件，只要遇见不满足条件的事件，则该事件以及之后的事件（不管是否满足条件）都会发出。

```swift
Observable.of(1,2,3,4,5,6).skipWhile({
            $0 < 3
        }).subscribe(onNext: {
            print($0)
        }).disposed(by: disposeBag)
```

打印结果与上面相同

## skipWhileWithIndex

跳过索引满足条件的事件。

```swift
Observable.of(1,2,3,4,5,6).skipWhileWithIndex({ (element, index) -> Bool in
            index < 3
        }).subscribe(onNext: {print($0)}).disposed(by: disposeBag)
```

打印结果：

```swift
4
5
6
```

> 此方法在新版本中已经被抛弃
>
> ## skipUntil
>
> 跳过另一个Observable序列发出事件之前的所有事件。与takeUntil相反。

```swift
let orinSubject = PublishSubject<Int>()
        let refreSubject = PublishSubject<Int>()
        orinSubject.skipUntil(refreSubject).subscribe(onNext: {
            print($0)
        }).disposed(by: disposeBag)

        orinSubject.onNext(1)
        orinSubject.onNext(2)
        refreSubject.onNext(10)
        orinSubject.onNext(3)
        orinSubject.onNext(4)
```

打印结果

```swift
3
4
```

