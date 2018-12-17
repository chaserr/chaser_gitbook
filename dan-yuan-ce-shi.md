1. OCMock对单利类mock无效
http://twobitlabs.com/2011/02/mocking-singletons-with-ocmock/

单元测试覆盖率报告

使用技术：lcov
gcno文件生成：运行单元测试即可:com+u
gcda文件生成:在.m中插入下面代码即可
```swift
int main(int argc, char * argv[]) {
    NSArray *paths =NSSearchPathForDirectoriesInDomains(NSDocumentDirectory,NSUserDomainMask, YES);
    NSString *documentsDirectory =[paths objectAtIndex:0];
    setenv("GCOV_PREFIX",[documentsDirectory cStringUsingEncoding:NSUTF8StringEncoding],1);
    setenv("GCOV_PREFIX_STRIP","13",1);

    @autoreleasepool {
        return UIApplicationMain(argc, argv, nil, NSStringFromClass([HMAppDelegate class]));
    }
}

```
使用步骤：
1. 拷贝gcno和gcda文件
cp /Users/tongxing/Library/Developer/CoreSimulator/Devices/011831B0-52FF-4842-AD5E-91FF6565F009/data/Containers/Data/Application/E378F577-BBB9-4C01-A831-07056AD90AC2/Documents/x86_64/*gcda /Users/tongxing/Desktop/codeCoverage

cp /Users/tongxing/Library/Developer/Xcode/DerivedData/MiDongTrainingCenter-ddlgsjcuhaugmzfhasredqhribms/Build/Intermediates.noindex/MiDongTrainingCenter.build/Debug-iphonesimulator/MiDongTrainingCenter.build/Objects-normal/x86_64/*.gcda /Users/tongxing/Desktop/codeCoverage



2. 生成info信息
lcov -c -d /Users/tongxing/Desktop/codeCoverage  -b /Users/tongxing/HMProject/training -o /Users/tongxing/Desktop/coverage.info

3. 生成html报告
genhtml -t "code coverage repor" /Users/tongxing/Desktop/coverage.info -o /Users/tongxing/Desktop/codeCoverageReport