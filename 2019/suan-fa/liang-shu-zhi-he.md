1. 给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

示例:
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
    
解法：
```swift
    // 耗时测试
    let start = CACurrentMediaTime()
    let result = twoSum(bigArr, 16021)
    let end = CACurrentMediaTime()
    print("方法耗时为：\(end-start)")
    print("结果为：\(result)")
```

```swift
// 此解法 为一般解，两次循环 耗时较长。
func twoSum(_ nums: [Int], _ target: Int) -> [Int] {

            for (i, number1) in nums.enumerated() {
                var tmpNums = nums
                tmpNums.remove(at: i)
                for (j, number2) in tmpNums.enumerated() {
                    if target == number1 + number2 {
                        return [i, j]
                    }
                }
            }
            return []
        }
```

```swift
// 此解为最优解， 耗时少，
func twoSum(_ nums: [Int], _ target: Int) -> [Int] {
        var value_position: [Int: Int] = [:]
        for (i, number1) in nums.enumerated() {
            let otherValue = target - nums[i]
            let index = value_position[otherValue]
            if index != nil && index != i {
                print("result：\(nums[i])--\(nums[index!])")
                return [index!, i]
            }
            value_position[number1] = i
        }
        return []
    }
```