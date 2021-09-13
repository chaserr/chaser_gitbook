# e.g.1

## 查找重复的单词

```javascript
<!DOCTYPE html>
<html>
<body>
<script>
var str = "Is is the cost of of gasoline going up up";
var patt1 = /\b([a-z]+) \1\b/ig;
document.write(str.match(patt1));
</script>

</body>
</html>
```

`\b`匹配一个字边界，即字与空格间的位置。字边界元字符确保只检测整个单词 `[a-z]+`指定的包含一个或者多个字母，

所以`\b([a-z]+)`是匹配出来is，is 正则表达式后面的全局标记 `g` 指定将该表达式应用到输入字符串中能够查找到的尽可能多的匹配。 表达式的结尾处的不区分大小写 `i` 标记指定不区分大小写。

## 解析一个 url

```javascript
<!DOCTYPE html>
<html>
<body>
<script>
var str = "http://www.runoob.com:80/html/html-tutorial.html";
var patt1 = /(\w+):\/\/([^/:]+)(:\d*)?([^# ]*)/;
arr = str.match(patt1);
for (var i = 0; i < arr.length ; i++) {
    document.write(arr[i]);
    document.write("<br>");
}
</script>

</body>
</html>
```

第一个括号子表达式捕获 Web 地址的协议部分。该子表达式匹配在冒号和两个正斜杠前面的任何单词。其中\w 匹配字母或数字或下划线或汉字 等价于 ''。

第二个括号子表达式捕获地址的域地址部分。子表达式匹配 / 和 : 之外的一个或多个字符。

第三个括号子表达式捕获端口号（如果指定了的话）。该子表达式匹配冒号后面的零个或多个数字。只能重复一次该子表达式。

最后，第四个括号子表达式捕获 Web 地址指定的路径和 / 或页信息。该子表达式能匹配不包括 \# 或空格字符的任何字符序列。

将正则表达式应用到上面的 URI，各子匹配项包含下面的内容：

第一个括号子表达式包含"http" 第二个括号子表达式包含"www.runoob.com" 第三个括号子表达式包含":80" 第四个括号子表达式包含"/html/html-tutorial.html"

