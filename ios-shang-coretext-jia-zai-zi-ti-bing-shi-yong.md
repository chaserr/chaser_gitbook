
#iOS上CoreText加载字体并使用

font_path是字体的路径，tweak的话，应该是可以加载任意位置的字体文件.

```C
        CFStringRef font_path = (__bridge CFStringRef)([[NSBundle mainBundle] pathForResource:@"字体名" ofType:@"字体类型(ttc/ttf/otf)"]);
        CFURLRef font_url = CFURLCreateWithFileSystemPath(kCFAllocatorDefault, font_path, kCFURLPOSIXPathStyle, false);
        CGDataProviderRef dataProvider = CGDataProviderCreateWithURL(font_url);
CGFontRef theCGFont = CGFontCreateWithDataProvider(dataProvider);
CTFontRef theCTFont = CTFontCreateWithGraphicsFont(theCGFont,17.0f,NULL,nil);
        CFRelease(dataProvider);
        CFRelease(font_url);
        UIGraphicsBeginImageContextWithOptions((CGSize){100.f, 100.f}, NO, 0.0f);
[@"要画下来的文字" drawInRect:CGRectMake(0, 0, 100, 100) withAttributes:@{(id)kCTFontAttributeName: (__bridge id)theCTFont}];
        UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
        UIGraphicsEndImageContext();
        CFRelease(theCTFont);
```