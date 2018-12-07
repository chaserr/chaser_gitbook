关于 开机广告展现规则调整 其中每条广告展示概率问题

我分别模拟了1000次，100次的情况，其中广告出现概率大致符合预期:
> 假设有3条广告，a:30%，b:40%,c:50%,所以不展示广告概率：10%

```swift
    __block NSInteger number0_30 = 0;
    __block NSInteger number31_70 = 0;
    __block NSInteger number71_90 = 0;
    for (int i = 0; i<10; i++) {
        uint randomIndex = arc4random_uniform(99);
        [probabilityInterval enumerateObjectsUsingBlock:^(NSDictionary *obj, NSUInteger idx, BOOL *_Nonnull stop) {
            NSArray *currentAdvertProbabilityInterval = obj[@"advertProbabilityInterval"];
            NSInteger firstValue = [(NSNumber *) currentAdvertProbabilityInterval.firstObject integerValue];
            NSInteger lastValue = [(NSNumber *) currentAdvertProbabilityInterval.lastObject integerValue];
            if (randomIndex < lastValue && randomIndex > firstValue) {
                displayAddvert = (HMAdvert *) obj[@"advert"];
                if (firstValue == 0) {
                    number0_30 += 1;
                }else if (firstValue == 31) {
                    number31_70 += 1;
                }else if (firstValue == 71){
                    number71_90 += 1;
                }
                NSLog(@"%@", currentAdvertProbabilityInterval);
                *stop = YES;
            }
        }];
    }
    NSLog(@"30%%概率：%ld次 40%%概率：%ld次 20%%概率：%ld次", (long)number0_30,(long)number31_70,(long)number71_90);
```
模拟10次结果：

```swift
2018-11-21 19:25:21.187211+0800 MiFit[24423:5399673] 30%概率：3次 40%概率：4次 20%概率：1次
```

模拟100次结果：

```swift
2018-11-21 19:21:36.595913+0800 MiFit[24403:5397551] 30%概率：32次 40%概率：36次 20%概率：15次
```

模拟1000次结果：

```swift
2018-11-21 19:23:08.572751+0800 MiFit[24410:5398373] 30%概率：277次 40%概率：395次 20%概率：175次
```

---


> 【注】以下情况无法保证

假设：3条广告 a:20%,b:30%,c:50%

1. 用户打开10次，恰好有与以上概率相对应的次数出现。

望各位测试知悉。