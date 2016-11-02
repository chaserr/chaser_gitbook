###Swift获取命名空间
``` objc 

//获取命名空间 
let spaceName = NSBundle.mainBundle().infoDictionary!["CFBundleExecutable"] as! String
```