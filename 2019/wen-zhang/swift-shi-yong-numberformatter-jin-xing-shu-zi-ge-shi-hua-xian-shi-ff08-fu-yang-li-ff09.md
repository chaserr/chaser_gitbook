# Swift - 使用NumberFormatter进行数字格式化显示（附样例）

## 1. 将数字转成字符串

1. 最简单的便是使用 NumberFormatter 的 localizedString 方法进行数字格式化

* number 属性可以设置相应的显示样式。
* from 属性为需要格式化的数字（必须为 NSNumber 类型）

```swift
/// 将数字转成字符串
func test() {

        //原始数字（需要先转成NSNumber类型）
        let number = NSNumber(value: 1234.5678)
        // 四舍五入的整数
        let none = NumberFormatter.localizedString(from: number, number: .none)
        //小数形式（以国际化格式输出 保留三位小数,第四位小数四舍五入）
        let decimal = NumberFormatter.localizedString(from: number, number: .decimal)
        //百分数形式
        let percent = NumberFormatter.localizedString(from: number, number: .percent)
        //科学计数
        let scientific = NumberFormatter.localizedString(from: number, number: .scientific)
        //朗读形式（英文表示）
        let spellOut = NumberFormatter.localizedString(from: number, number: .spellOut)
        //序数形式
        let ordinal = NumberFormatter.localizedString(from: number, number: .ordinal)
        //货币形式（以货币通用格式输出 保留2位小数,第三位小数四舍五入,在前面添加货币符号）
        let currency = NumberFormatter.localizedString(from: number, number: .currency)
        //货币形式
        let currencyISOCode = NumberFormatter.localizedString(from: number,
                                                              number: .currencyISOCode)
        //货币形式
        let currencyPlural = NumberFormatter.localizedString(from: number,
                                                             number: .currencyPlural)
        //会计计数
        let currencyAccounting = NumberFormatter.localizedString(from: number,
                                                                 number: .currencyAccounting)

        //打印结果
        print("原始值 = \(number)")
        print("none = \(none)")
        print("decimal = \(decimal)")
        print("percent = \(percent)")
        print("scientific = \(scientific)")
        print("spellOut = \(spellOut)")
        print("ordinal = \(ordinal)")
        print("currency = \(currency)")
        print("currencyISOCode = \(currencyISOCode)")
        print("currencyPlural = \(currencyPlural)")
        print("currencyAccounting = \(currencyAccounting)")

        /**
         打印结果
         原始值 = 1234.5678
         none = 1235
         decimal = 1,234.568
         percent = 123,457%
         scientific = 1.2345678E3
         spellOut = one thousand two hundred thirty-four point five six seven eight
         ordinal = 1,235th
         currency = $1,234.57
         currencyISOCode = USD1,234.57
         currencyPlural = 1,234.57 US dollars
         currencyAccounting = $1,234.57
        */

    }
```

## 2.将字符串转为数字

1. NumberFormatter 除了可以将数字转成字符串，还可以将字符串转为数字。

```swift
/// 将字符串转成数字
    func test1() {
        //从字符串装成数字
        let number = NumberFormatter().number(from: "1234.5678")!
        //四舍五入的整数
        let none = NumberFormatter.localizedString(from: number, number: .none)
        //小数形式（以国际化格式输出 保留三位小数,第四位小数四舍五入）
        let decimal = NumberFormatter.localizedString(from: number, number: .decimal)

        //打印结果
        print("原始值 = \(number)")
        print("none = \(none)")
        print("decimal = \(decimal)")
        /**
         打印结果
         原始值 = 1234.5678
         none = 1235
         decimal = 1,234.568
         */
    }
```

除了使用 NumberFormatter 的 localizedString 方法提供的几种格式化方式外。我们也可以设置 NumberFormatter 对象的相应属性，来实现定制。

## 3. 修改分割符、分割位数

```swift
/// 修改分隔符，分割位数
    func test3() {
        //原始值
        let number = NSNumber(value: 12345.6789)

        //创建一个NumberFormatter对象
        let numberFormatter = NumberFormatter()
        //设置number显示样式
        numberFormatter.numberStyle = .decimal  // 小数形式
        numberFormatter.usesGroupingSeparator = true //设置用组分隔
        numberFormatter.groupingSeparator = "," //分隔符号
        numberFormatter.groupingSize = 4  //分隔位数
        //格式化
        let format = numberFormatter.string(from: number)!

        //打印结果
        print("原始值 = \(number)")
        print("格式化结果 = \(format)")
        /**
         打印结果
         原始值 = 12345.6789
         格式化结果 = 1,2345.679
         */
    }
```

## 4. 设置格式宽度、填充符、填充位置

```swift
/// 设置格式宽度、填充符、填充位置
    func test4() {
        //原始值
        let number = NSNumber(value: 12345.6789)

        //创建一个NumberFormatter对象
        let numberFormatter = NumberFormatter()
        //设置number显示样式
        numberFormatter.numberStyle = .none //四舍五入的整数
        numberFormatter.formatWidth = 10 //补齐10位
        numberFormatter.paddingCharacter = "0" //不足位数用0补
        numberFormatter.paddingPosition = .beforePrefix  //补在前面
        //格式化
        let format = numberFormatter.string(from: number)!

        //打印结果
        print("原始值 = \(number)")
        print("格式化结果 = \(format)")
        /**
         打印结果
         原始值 = 12345.6789
         格式化结果 = 0000012346
         */
    }
```

## 5. 设置最大整数位数、最小整数位数

```swift
/// 设置最大整数位数、最小整数位数
    func test5() {
        //原始值
        let number = NSNumber(value: 5.6789)

        //创建一个NumberFormatter对象
        let numberFormatter = NumberFormatter()
        //设置number显示样式
        numberFormatter.numberStyle = .none //四舍五入的整数
        numberFormatter.maximumIntegerDigits = 4 //设置最大整数位数（超过的直接截断）
        numberFormatter.minimumIntegerDigits = 1 //设置最小整数位数（不足的前面补0）
        //格式化
        let format = numberFormatter.string(from: number)!

        //打印结果
        print("原始值 = \(number)")
        print("格式化结果 = \(format)")
        /**
         打印结果
         原始值 = 5.6789
         格式化结果 = 6
         */
    }
```

## 6. 设置最大小数位数、最小小数位数

```swift
/// 设置最大小数位数、最小小数位数
    func test8() {
        //原始值
        let number = NSNumber(value: 12345.6)

        //创建一个NumberFormatter对象
        let numberFormatter = NumberFormatter()
        numberFormatter.maximumFractionDigits = 3 //设置小数点后最多3位
        numberFormatter.minimumFractionDigits = 2 //设置小数点后最少2位（不足补0）
        //格式化
        let format = numberFormatter.string(from: number)!

        //打印结果
        print("原始值 = \(number)")
        print("格式化结果 = \(format)")
    }
```

## 7. 设置前缀、后缀

```swift
/// 设置前缀、后缀
    func test6() {
        //原始值
        let number = NSNumber(value: 12345.6789)

        //创建一个NumberFormatter对象
        let numberFormatter = NumberFormatter()
        //设置number显示样式
        numberFormatter.numberStyle = .none //四舍五入的整数
        numberFormatter.positivePrefix = "$" //自定义前缀
        numberFormatter.positiveSuffix = "元" //自定义后缀
        //格式化
        let format = numberFormatter.string(from: number)!

        //打印结果
        print("原始值 = \(number)")
        print("格式化结果 = \(format)")
        /**
         打印结果
         原始值 = 12345.6789
         格式化结果 = $12346元
         */
    }
```

## 8. 设置格式化字符串

```swift
/// 设置格式化字符串

    func test7() {
        //原始值
        let number = NSNumber(value: -12345.6789)

        //创建一个NumberFormatter对象
        let numberFormatter = NumberFormatter()
        numberFormatter.positiveFormat = "##,##,##.##" //设置格式
        //格式化
        let format = numberFormatter.string(from: number)!

        //打印结果
        print("原始值 = \(number)")
        print("格式化结果 = \(format)")
        /**
         打印结果
         原始值 = -12345.6789
         格式化结果 = -1,23,45.68
         */
    }
```

