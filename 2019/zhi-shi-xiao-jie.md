
为了获取一些堆栈信息以便于快准狠的定位问题，可以在main函数里：

int main(int argc, char * argv[]) {
 @try {
  @autoreleasepool
  {
   return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
  }
 }
 @catch (NSException* exception)
 {
  NSDebugLog(@"Exception=%@\nStack Trace:%@", exception, [exception callStackSymbols]);
 }
}