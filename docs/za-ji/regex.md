# 正则表达式

## 字符匹配

1. 精准匹配

```javascript
const regex = /hello/
regex.test('hello')
// true
```

1. 模糊匹配

| 模式 | 写法 | 介绍 |
| :--- | :--- | :--- |
| 横向模糊匹配 | {m, n} | 连续出现的次数，最少m，最多n |
| 纵向模糊匹配 | \[abc\] | abc其中一个 |

* 纵向匹配范围表示法

  `[123456abcdefGHIJKLM]`，可以写成`[1-6a-fG-M]`。用连字符`-`来省略和简写。

* 纵向匹配排除字符串

  `[^abc]`，表示是一个除"a"、"b"、"c"之外的任意一个字符。字符组的第一位放`^`（脱字符），表示求反的概念。

* 纵向模糊匹配简写形式

| 简写形式 | 常规写法 | 描述 |
| :--- | :--- | :--- |
| \d | \[0-9\] | 一位数字（digit） |
| \D |  | 一位非数字 |
| \w | \[0-9a-zA-Z\_\] | 表示数字、大小写字母和下划线\(word\) |
| \W |  | 非单词字符 |
| \s | \[ \t\v\n\r\f\] | 空白符，包括空格、水平制表符、垂直制表符、换行符、回车符、换页符\(space\) |
| \S |  | 非空白符 |
| . |  | 通配符 |

* 横向模糊匹配简写

| 简写形式 | 常规写法 | 描述 |
| :--- | :--- | :--- |
| {m,} |  | 至少出现m次 |
| {m} | {m,m} | 出现m次 |
| ? | {0,1} | 可出现可不出现 |
| + | {1,} | 至少出现一次 |
| \* | {0,} | 可出现任意次 |

1. 惰性匹配与贪婪匹配

在正常情况下的正则都是贪婪的

```javascript
var regex = /a.*c/g;
var string = "abbcbbbbc";
console.log( string.match(regex) ); 
// ["abbcbbbbc"]
```

贪婪的意思就是尽量匹配更多的字符，在第一次匹配到所需字符后（\[abbc\]）不会停止，会继续向后匹配直到最后一个符合条件的字符（\["abbcbbbbc"\]）

对应惰性匹配符

```javascript
var regex = /a.*?c/g;
var string = "abbcbbbbc";
console.log( string.match(regex) ); 
// ["abbc"]
```

惰性匹配则会在匹配第一个达到条件的字符后停止，除了满足特定需求外还，这里还会减少字符回溯的操作，提升正则性能，所以在可以的情况下推荐使用惰性匹配符

1. 多选分支

多选结构用\|连接，表示可以任选由\|连接的多个字符中的任意一个

**需要注意的是，由\|的优先级是最低的，在考虑字符匹配的时候需要最后判断，而且是\|是惰性的，放在前面的字符优先级更高，而且在初次匹配后会停止**

​ 4.1 基本分支结构

```javascript
var regex = /good|nice/g;
var string = "good idea, nice try.";
console.log( string.match(regex) ); 
// => ["good", "nice"]
```

​ 4.2 特性一：惰性匹配，前面的优先级更高

```javascript
// wrong
var regex = /good|goodbye/g;
var string = "goodbye";
console.log( string.match(regex) ); 
// => ["good"]
// success
var regex = /goodbye|good/g;
var string = "goodbye";
console.log( string.match(regex) ); 
// => ["goodbye"]
```

​ 4.3 特性二：管道符号永远优先级最低

_这里简单提一下后面会详细说_

```javascript
// /acc|b*/ !== /(acc|b)*/

// /acc|b*/ === /acc|(b*)/
```

## 位置匹配

| 字符 | 描述 |
| :--- | :--- |
| ^ | 匹配字符串开头 |
| $ | 匹配字符串结尾 |
| \b | 字符边界（\w与\W, \w与^, \w与$之间的位置） |
| \B | 除了上面的位置外的所有位置 |
| \(?=p\) | p前面的位置 |
| \(?!p\) | 除了p前面外的所有位置 |

首先，位置的概念，在正则中位置的概念都可以理解为每个字符的中间都有一个空字符连接，而位置匹配就是匹配中间这些位置

例：hello =&gt; h + "" + e + "" + l + "" + l + "" + o

其中后面四个都很好理解，不会有太多复杂的应用

```javascript
var result = "[JS] Lesson_01.mp4".replace(/\b/g, '#');
console.log(result); 
// => "[#JS#] #Lesson_01#.#mp4#"
var result = "[JS] Lesson_01.mp4".replace(/\B/g, '#');
console.log(result); 
// => "#[J#S]# L#e#s#s#o#n#_#0#1.m#p#4"
var result = "hello".replace(/(?=l)/g, '#');
console.log(result); 
// => "he#l#lo"
var result = "hello".replace(/(?!l)/g, '#');
console.log(result); 
// => "#h#ell#o#"
```

其中^ 和 $的使用会复杂些

这两个匹配符本身很好理解，匹配开头和结尾

```javascript
//以ab开头
var regex = /^ab/
regex.test('abaaa')
// true
regex.test('baaaa')
// false

//以ab结尾
var regex = /ab$/
regex.test('abaaab')
// true
regex.test('baaaa')
// false
```

> 应用一：匹配全字符（匹配完整字符）

```javascript
var regex = /^hello$/
```

> 应用二：逆向匹配（确保字符串中未达成某条件）

```javascript
// 不可以全部是数字
var regex = /(?!^[0-9]*$)^/
regex.test('1111')
// true
regex.test('111r')
// false
```

**例子：**

> 密码长度6-12位，由数字、小写字符和大写字母组成，但必须至少包括2种字符。

分析：这个问题应该拆分为 由6-12位数字、大小写字母组成 + 至少包含两种字符

```javascript
// 由6-12位数字、大小写字母组成
var reg = /^[0-9A-Za-z]{6,12}$/;
// 至少包含某种字符
var reg = /(?=.*[0-9])^/
// 结合一下，至少包含数字和小写字符的6-12位字符
var reg = /(?=.*[0-9])(?=.*[a-z])^[0-9A-Za-z]{6,12}$/;
// 结合每种情况
var reg = /((?=.*[0-9])(?=.*[a-z])|(?=.*[0-9])(?=.*[A-Z])|(?=.*[a-z])(?=.*[A-Z]))^[0-9A-Za-z]{6,12}$/;
console.log( reg.test("1234567") ); // false 全是数字
console.log( reg.test("abcdef") ); // false 全是小写字母
console.log( reg.test("ABCDEFGH") ); // false 全是大写字母
console.log( reg.test("ab23C") ); // false 不足6位
console.log( reg.test("ABCDEF234") ); // true 大写字母和数字
console.log( reg.test("abcdEF234") ); // true 三者都有
```

或者说，这个正则可以理解为不能只是某一种字符

```javascript
// 不能全是数字
var reg = /(?!^[0-9]{6,12}$)^[0-9A-Za-z]{6,12}$/;
// 最终答案
var reg = /(?!^[0-9]{6,12}$)(?!^[a-z]{6,12}$)(?!^[A-Z]{6,12}$)^[0-9A-Za-z]{6,12}$/;
console.log( reg.test("1234567") ); // false 全是数字
console.log( reg.test("abcdef") ); // false 全是小写字母
console.log( reg.test("ABCDEFGH") ); // false 全是大写字母
console.log( reg.test("ab23C") ); // false 不足6位
console.log( reg.test("ABCDEF234") ); // true 大写字母和数字
console.log( reg.test("abcdEF234") ); // true 三者都有
```

## 括号作用

首先是最符合直觉的作用分组

```javascript
// 匹配ab字符
var regex = /(ab+)/g

// 分支匹配
var regex = /^I love (JavaScript|Regular Expression)$/;
console.log( regex.test("I love JavaScript") );
console.log( regex.test("I love Regular Expression") );
// => true
// => true
```

重点：引用分组，括号包住的部分可以进行数据的提取

```javascript
var regex = /(\d{4})-(\d{2})-(\d{2})/;
var string = "2017-06-12";
console.log( string.match(regex) ); 
// => ["2017-06-12", "2017", "06", "12", index: 0, input: "2017-06-12"]
```

也可以使用构造函数的全局属性$1-$9来获取

```javascript
var regex = /(\d{4})-(\d{2})-(\d{2})/;
var string = "2017-06-12";

regex.test(string); // 正则操作即可，例如
//regex.exec(string);
//string.match(regex);

console.log(RegExp.$1); // "2017"
console.log(RegExp.$2); // "06"
console.log(RegExp.$3); // "12"
```

相关的内置方法也可以获取相关数值

```javascript
var regex = /(\d{4})-(\d{2})-(\d{2})/;
var string = "2017-06-12";
var result = string.replace(regex, "$2/$3/$1");
console.log(result); 
// => "06/12/2017"
```

等价于

```javascript
var regex = /(\d{4})-(\d{2})-(\d{2})/;
var string = "2017-06-12";
var result = string.replace(regex, function(match, year, month, day) {
    return month + "/" + day + "/" + year;
});
console.log(result); 
// => "06/12/2017"
```

在表达式内部也可以通过反向引用的方式来获取到当前表达式的值来应用于匹配相关数值

> 2016-06-12
>
> 2016/06/12
>
> 2016.06.12

这里的难点在于如何确保前后两个连接符是一致的，可以用反向匹配（\num）来匹配

```javascript
var regex = /\d{4}(-|\/|\.)\d{2}\1\d{2}/;
var string1 = "2017-06-12";
var string2 = "2017/06/12";
var string3 = "2017.06.12";
var string4 = "2016-06/12";
console.log( regex.test(string1) ); // true
console.log( regex.test(string2) ); // true
console.log( regex.test(string3) ); // true
console.log( regex.test(string4) ); // false
```

**这里的数字可以高于一位\10 代表第十位**

​ 一般来讲所有括号内的值都会被捕获和引用，但是你也可以采用非捕获分组的方式来使用括号**\(?:p\)**，使用非捕获分组时括号就仅仅作为分组，而不会被捕获，所以也没办法被反向引用等

​ 一般来讲非捕获分组的主要作用在于可以提高表达式效率，当你的表达式较为复杂且经常被调用时应该考虑使用非捕获分组来提高运行效率

```javascript
var regex = /(?:ab)+/g;
var string = "ababa abbb ababab";
console.log( string.match(regex) ); 
// => ["abab", "ab", "ababab"]
```

## 正则的匹配顺序

​ 正则在匹配的时候由左向右执行，转义符最优先，其次是括号，匹配顺序总体来说是符合直觉的，可能会反直觉的主要是 \|符号，他的优先级最低，低于所有匹配符，在匹配顺序上他约等同于\|符号两边的匹配符都加上括号

举例来说

```javascript
var regexp = /^abc|bcd$/
var regexp1 = /(^abc)|(bcd$)/
var regexp2 = /^ab(c|b)cd$/
var regexp3 = /^(abc)|(bcd)$/

regexp == regexp1 != regexp2 != regexp3
```

## 正则表达式的优化

​ 正则表达式在匹配的时候是贪婪的，贪婪的匹配必然会面临着回溯的问题

> /".\*"/

![&#x8868;&#x8FBE;&#x5F0F;&#x56DE;&#x6EAF;&#x6D41;&#x7A0B;](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2017/7/19/33b738b42fc9e74edc84b17479ac7000~tplv-t2oaga2asx-image.image)

​ 简单来讲回溯可以理解为表达式匹配会尽量匹配最多的字符，当无法进一步匹配的时候就会产生回溯，回头走几步，尝试另一种尽可能的匹配，在不断的前进和回溯的过程中寻找最贪婪的匹配方法

​ 而优化的表达式的行为中最有效的就是一般就是减少这种回溯的行为，当然除却减少回溯之外还有使用非捕获分组，简化表达式等等。。这些方法要么很简单易懂要么不好描述，其中最简单易懂的就是减少回溯的行为

​ 再回到刚才那张图

> /".\*"/

​ 这个表达式中.\*是一个最容易产生回溯的表达式之一，他会匹配到最后一个字符，然后一步一步的回溯直到找到最贪婪的匹配项

​ 可以优化为

> /"\*"/

​ 优化后的表达式中就减少了不必要的回溯行为，在匹配到结束的"会立即截止

​ 同理使用惰性匹配符也可以减少匹配符的使用\(量词+括号\)

> ```text
> {m,n}?
> {m,}?
> ??
> +?
> *?
> ```

## JavaSctipt中相关的正则相关方法

> 在js中与正则相关的功能主要有四种

**1.1 验证**

验证是正则表达式最直接的应用，比如表单验证。

在说验证之前，先要说清楚匹配是什么概念。

所谓匹配，就是看目标字符串里是否有满足匹配的子串。因此，“匹配”的本质就是“查找”。

有没有匹配，是不是匹配上，判断是否的操作，即称为“验证”。

这里举一个例子，来看看如何使用相关API进行验证操作的。

比如，判断一个字符串中是否有数字。

* 使用`search`

```javascript
var regex = /\d/;
var string = "abc123";
console.log( !!~string.search(regex) );
// => true
```

* 使用`test`

```javascript
var regex = /\d/;
var string = "abc123";
console.log( regex.test(string) );
// => true
```

* 使用`match`

```javascript
var regex = /\d/;
var string = "abc123";
console.log( !!string.match(regex) );
// => true
```

* 使用`exec`

```javascript
var regex = /\d/;
var string = "abc123";
console.log( !!regex.exec(string) );
// => true
```

其中，最常用的是`test`。

**1.2 切分**

匹配上了，我们就可以进行一些操作，比如切分。

所谓“切分”，就是把目标字符串，切成一段一段的。在JS中使用的是`split`。

比如，目标字符串是"html,css,javascript"，按逗号来切分：

```javascript
var regex = /,/;
var string = "html,css,javascript";
console.log( string.split(regex) );
// => ["html", "css", "javascript"]
```

又比如，如下的日期格式：

> 2017/06/26
>
> 2017.06.26
>
> 2017-06-26

可以使用`split`“切出”年月日：

```javascript
var regex = /\D/;
console.log( "2017/06/26".split(regex) );
console.log( "2017.06.26".split(regex) );
console.log( "2017-06-26".split(regex) );
// => ["2017", "06", "26"]
// => ["2017", "06", "26"]
// => ["2017", "06", "26"]
```

**1.3 提取**

虽然整体匹配上了，但有时需要提取部分匹配的数据。

此时正则通常要使用分组引用（分组捕获）功能，还需要配合使用相关API。

这里，还是以日期为例，提取出年月日。注意下面正则中的括号：

* `match`

```javascript
var regex = /^(\d{4})\D(\d{2})\D(\d{2})$/;
var string = "2017-06-26";
console.log( string.match(regex) );
// =>["2017-06-26", "2017", "06", "26", index: 0, input: "2017-06-26"]
```

* `exec`

```javascript
var regex = /^(\d{4})\D(\d{2})\D(\d{2})$/;
var string = "2017-06-26";
console.log( regex.exec(string) );
// =>["2017-06-26", "2017", "06", "26", index: 0, input: "2017-06-26"]
```

* `test`

```javascript
var regex = /^(\d{4})\D(\d{2})\D(\d{2})$/;
var string = "2017-06-26";
regex.test(string);
console.log( RegExp.$1, RegExp.$2, RegExp.$3 );
// => "2017" "06" "26"
```

* `search`

```javascript
var regex = /^(\d{4})\D(\d{2})\D(\d{2})$/;
var string = "2017-06-26";
string.search(regex);
console.log( RegExp.$1, RegExp.$2, RegExp.$3 );
// => "2017" "06" "26"
```

* `replace`

```javascript
var regex = /^(\d{4})\D(\d{2})\D(\d{2})$/;
var string = "2017-06-26";
var date = [];
string.replace(regex, function(match, year, month, day) {
    date.push(year, month, day);
});
console.log(date);
// => ["2017", "06", "26"]
```

其中，最常用的是`match`。

**1.4 替换**

找，往往不是目的，通常下一步是为了替换。在JS中，使用`replace`进行替换。

比如把日期格式，从yyyy-mm-dd替换成yyyy/mm/dd：

```javascript
var string = "2017-06-26";
var today = new Date( string.replace(/-/g, "/") );
console.log( today );
// => Mon Jun 26 2017 00:00:00 GMT+0800 (中国标准时间)
```

这里只是简单地应用了一下`replace`。但，`replace`方法是强大的，是需要重点掌握的。

## 相关API注意要点

从上面可以看出用于正则操作的方法，共有6个，字符串实例4个，正则实例2个：

> String\#search
>
> String\#split
>
> String\#match
>
> String\#replace
>
> RegExp\#test
>
> RegExp\#exec

本文不打算详细地讲解它们的方方面面细节，具体可以参考《JavaScript权威指南》的第三部分。本文重点列出一些容易忽视的地方，以飨读者。

**2.1 search和match的参数问题**

我们知道字符串实例的那4个方法参数都支持正则和字符串。

但`search`和`match`，会把字符串转换为正则的。

```javascript
var string = "2017.06.27";

console.log( string.search(".") );
// => 0
//需要修改成下列形式之一
console.log( string.search("\\.") );
console.log( string.search(/\./) );
// => 4
// => 4

console.log( string.match(".") );
// => ["2", index: 0, input: "2017.06.27"]
//需要修改成下列形式之一
console.log( string.match("\\.") );
console.log( string.match(/\./) );
// => [".", index: 4, input: "2017.06.27"]
// => [".", index: 4, input: "2017.06.27"]

console.log( string.split(".") );
// => ["2017", "06", "27"]

console.log( string.replace(".", "/") );
// => "2017/06.27"
```

**2.2 match返回结果的格式问题**

`match`返回结果的格式，与正则对象是否有修饰符`g`有关。

```javascript
var string = "2017.06.27";
var regex1 = /\b(\d+)\b/;
var regex2 = /\b(\d+)\b/g;
console.log( string.match(regex1) );
console.log( string.match(regex2) );
// => ["2017", "2017", index: 0, input: "2017.06.27"]
// => ["2017", "06", "27"]
```

没有`g`，返回的是标准匹配格式，即，数组的第一个元素是整体匹配的内容，接下来是分组捕获的内容，然后是整体匹配的第一个下标，最后是输入的目标字符串。

有`g`，返回的是所有匹配的内容。

当没有匹配时，不管有无`g`，都返回`null`。

**2.3 exec比match更强大**

当正则没有`g`时，使用`match`返回的信息比较多。但是有`g`后，就没有关键的信息`index`了。

而`exec`方法就能解决这个问题，它能接着上一次匹配后继续匹配：

```javascript
var string = "2017.06.27";
var regex2 = /\b(\d+)\b/g;
console.log( regex2.exec(string) );
console.log( regex2.lastIndex);
console.log( regex2.exec(string) );
console.log( regex2.lastIndex);
console.log( regex2.exec(string) );
console.log( regex2.lastIndex);
console.log( regex2.exec(string) );
console.log( regex2.lastIndex);
// => ["2017", "2017", index: 0, input: "2017.06.27"]
// => 4
// => ["06", "06", index: 5, input: "2017.06.27"]
// => 7
// => ["27", "27", index: 8, input: "2017.06.27"]
// => 10
// => null
// => 0
```

其中正则实例`lastIndex`属性，表示下一次匹配开始的位置。

比如第一次匹配了“2017”，开始下标是0，共4个字符，因此这次匹配结束的位置是3，下一次开始匹配的位置是4。

从上述代码看出，在使用`exec`时，经常需要配合使用`while`循环：

```javascript
var string = "2017.06.27";
var regex2 = /\b(\d+)\b/g;
var result;
while ( result = regex2.exec(string) ) {
    console.log( result, regex2.lastIndex );
}
// => ["2017", "2017", index: 0, input: "2017.06.27"] 4
// => ["06", "06", index: 5, input: "2017.06.27"] 7
// => ["27", "27", index: 8, input: "2017.06.27"] 10
```

**2.4 修饰符g，对exex和test的影响**

上面提到了正则实例的`lastIndex`属性，表示尝试匹配时，从字符串的`lastIndex`位开始去匹配。

字符串的四个方法，每次匹配时，都是从0开始的，即`lastIndex`属性始终不变。

而正则实例的两个方法`exec`、`test`，当正则是全局匹配时，每一次匹配完成后，都会修改`lastIndex`。下面让我们以`test`为例，看看你是否会迷糊：

```javascript
var regex = /a/g;
console.log( regex.test("a"), regex.lastIndex );
console.log( regex.test("aba"), regex.lastIndex );
console.log( regex.test("ababc"), regex.lastIndex );
// => true 1
// => true 3
// => false 0
```

注意上面代码中的第三次调用`test`，因为这一次尝试匹配，开始从下标`lastIndex`即3位置处开始查找，自然就找不到了。

如果没有`g`，自然都是从字符串第0个字符处开始尝试匹配：

```javascript
var regex = /a/;
console.log( regex.test("a"), regex.lastIndex );
console.log( regex.test("aba"), regex.lastIndex );
console.log( regex.test("ababc"), regex.lastIndex );
// => true 0
// => true 0
// => true 0
```

**2.5 test整体匹配时需要使用^和$**

这个相对容易理解，因为`test`是看目标字符串中是否有子串匹配正则，即有部分匹配即可。

如果，要整体匹配，正则前后需要添加开头和结尾：

```javascript
console.log( /123/.test("a123b") );
// => true
console.log( /^123$/.test("a123b") );
// => false
console.log( /^123$/.test("123") );
// => true
```

**2.6 split相关注意事项**

`split`方法看起来不起眼，但要注意的地方有两个的。

第一，它可以有第二个参数，表示结果数组的最大长度：

```javascript
var string = "html,css,javascript";
console.log( string.split(/,/, 2) );
// =>["html", "css"]
```

第二，正则使用分组时，结果数组中是包含分隔符的：

```javascript
var string = "html,css,javascript";
console.log( string.split(/(,)/) );
// =>["html", ",", "css", ",", "javascript"]
```

**2.7 replace是很强大的**

《JavaScript权威指南》认为`exec`是这6个API中最强大的，而我始终认为`replace`才是最强大的。因为它也能拿到该拿到的信息，然后可以假借替换之名，做些其他事情。

总体来说`replace`有两种使用形式，这是因为它的第二个参数，可以是字符串，也可以是函数。

当第二个参数是字符串时，如下的字符有特殊的含义：

> `$1`,`$2`,...,`$99`匹配第1~99个分组里捕获的文本 `$&` 匹配到的子串文本 ```$`` 匹配到的子串的左边文本```$'`匹配到的子串的右边文本`?\` 美元符号

例如，把"2,3,5"，变成"5=2+3"：

```javascript
var result = "2,3,5".replace(/(\d+),(\d+),(\d+)/, "$3=$1+$2");
console.log(result);
// => "5=2+3"
```

又例如，把"2,3,5"，变成"222,333,555":

```javascript
var result = "2,3,5".replace(/(\d+)/g, "$&$&$&");
console.log(result);
// => "222,333,555"
```

再例如，把"2+3=5"，变成"2+3=2+3=5=5":

```javascript
var result = "2+3=5".replace(/=/, "$&$`$&$'$&");
console.log(result);
// => "2+3=2+3=5=5"
```

当第二个参数是函数时，我们需要注意该回调函数的参数具体是什么：

```javascript
"1234 2345 3456".replace(/(\d)\d{2}(\d)/g, function(match, $1, $2, index, input) {
    console.log([match, $1, $2, index, input]);
});
// => ["1234", "1", "4", 0, "1234 2345 3456"]
// => ["2345", "2", "5", 5, "1234 2345 3456"]
// => ["3456", "3", "6", 10, "1234 2345 3456"]
```

此时我们可以看到`replace`拿到的信息，并不比`exec`少。

**2.8 使用构造函数需要注意的问题**

一般不推荐使用构造函数生成正则，而应该优先使用字面量。因为用构造函数会多写很多`\`。

```javascript
var string = "2017-06-27 2017.06.27 2017/06/27";
var regex = /\d{4}(-|\.|\/)\d{2}\1\d{2}/g;
console.log( string.match(regex) );
// => ["2017-06-27", "2017.06.27", "2017/06/27"]

regex = new RegExp("\\d{4}(-|\\.|\\/)\\d{2}\\1\\d{2}", "g");
console.log( string.match(regex) );
// => ["2017-06-27", "2017.06.27", "2017/06/27"]
```

**2.9 修饰符**

ES5中修饰符，共3个：

> `g` 全局匹配，即找到所有匹配的，单词是global
>
> `i` 忽略字母大小写，单词ingoreCase
>
> `m` 多行匹配，只影响`^`和`$`，二者变成行的概念，即行开头和行结尾。单词是multiline

当然正则对象也有相应的只读属性：

```javascript
var regex = /\w/img;
console.log( regex.global );
console.log( regex.ignoreCase );
console.log( regex.multiline );
// => true
// => true
// => true
```

**2.10 source属性**

正则实例对象属性，除了`global`、`ingnoreCase`、`multiline`、`lastIndex`属性之外，还有一个`source`属性。

它什么时候有用呢？

比如，在构建动态的正则表达式时，可以通过查看该属性，来确认构建出的正则到底是什么：

```javascript
var className = "high";
var regex = new RegExp("(^|\\s)" + className + "(\\s|$)");
console.log( regex.source )
// => (^|\s)high(\s|$) 即字符串"(^|\\s)high(\\s|$)"
```

**2.11 构造函数属性**

构造函数的静态属性基于所执行的最近一次正则操作而变化。除了是`$1`,...,`$9`之外，还有几个不太常用的属性（有兼容性问题）：

> ```text
> `RegExp.input` 最近一次目标字符串，简写成`RegExp["$_"]`
> `RegExp.lastMatch` 最近一次匹配的文本，简写成`RegExp["$&"]`
> `RegExp.lastParen` 最近一次捕获的文本，简写成`RegExp["$+"]`
> `RegExp.leftContext` 目标字符串中`lastMatch`之前的文本，简写成`RegExp["$`"]`
> `RegExp.rightContext `目标字符串中`lastMatch`之后的文本，简写成`RegExp["$'"]
> ```

测试代码如下：

```javascript
var regex = /([abc])(\d)/g;
var string = "a1b2c3d4e5";
string.match(regex);

console.log( RegExp.input );
console.log( RegExp["$_"]);
// => "a1b2c3d4e5"

console.log( RegExp.lastMatch );
console.log( RegExp["$&"] );
// => "c3"

console.log( RegExp.lastParen );
console.log( RegExp["$+"] );
// => "3"

console.log( RegExp.leftContext );
console.log( RegExp["$`"] );
// => "a1b2"

console.log( RegExp.rightContext );
console.log( RegExp["$'"] );
// => "d4e5"
```

## 参考

[https://juejin.cn/post/6844903487155732494\#heading-39](https://juejin.cn/post/6844903487155732494#heading-39)

## 正则可视化

[https://jex.im/regulex/\#](https://jex.im/regulex/#)

