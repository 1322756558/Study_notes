## 正则表达式

### 字符匹配

1. 精准匹配

```js
const regex = /hello/
regex.test('hello')
// true
```

2. 模糊匹配

| 模式         | 写法   | 介绍                         |
| ------------ | ------ | ---------------------------- |
| 横向模糊匹配 | {m, n} | 连续出现的次数，最少m，最多n |
| 纵向模糊匹配 | [abc]  | abc其中一个                  |

- 纵向匹配范围表示法

  `[123456abcdefGHIJKLM]`，可以写成`[1-6a-fG-M]`。用连字符`-`来省略和简写。

- 纵向匹配排除字符串

  `[^abc]`，表示是一个除"a"、"b"、"c"之外的任意一个字符。字符组的第一位放`^`（脱字符），表示求反的概念。

- 纵向模糊匹配简写形式

| 简写形式 | 常规写法       | 描述                                                         |
| -------- | -------------- | ------------------------------------------------------------ |
| \d       | [0-9]          | 一位数字（digit）                                            |
| \D       | [^0-9]         | 一位非数字                                                   |
| \w       | [0-9a-zA-Z_]   | 表示数字、大小写字母和下划线(word)                           |
| \W       | [^0-9a-zA-Z_]  | 非单词字符                                                   |
| \s       | [ \t\v\n\r\f]  | 空白符，包括空格、水平制表符、垂直制表符、换行符、回车符、换页符(space) |
| \S       | [^ \t\v\n\r\f] | 非空白符                                                     |
| .        |                | 通配符                                                       |

- 横向模糊匹配简写

| 简写形式 | 常规写法 | 描述           |
| -------- | -------- | -------------- |
| {m,}     |          | 至少出现m次    |
| {m}      | {m,m}    | 出现m次        |
| ?        | {0,1}    | 可出现可不出现 |
| +        | {1,}     | 至少出现一次   |
| *        | {0,}     | 可出现任意次   |

3. 惰性匹配与贪婪匹配

在正常情况下的正则都是贪婪的

```js
var regex = /a.*c/g;
var string = "abbcbbbbc";
console.log( string.match(regex) ); 
// ["abbcbbbbc"]
```

贪婪的意思就是尽量匹配更多的字符，在第一次匹配到所需字符后（[abbc]）不会停止，会继续向后匹配直到最后一个符合条件的字符（["abbcbbbbc"]）

对应惰性匹配符

```js
var regex = /a.*?c/g;
var string = "abbcbbbbc";
console.log( string.match(regex) ); 
// ["abbc"]
```

惰性匹配则会在匹配第一个达到条件的字符后停止，除了满足特定需求外还，这里还会减少字符回溯的操作，提升正则性能，所以在可以的情况下推荐使用惰性匹配符

4. 多选分支

多选结构用|连接，表示可以任选由|连接的多个字符中的任意一个

**需要注意的是，由|的优先级是最低的，在考虑字符匹配的时候需要最后判断，而且是|是惰性的，放在前面的字符优先级更高，而且在初次匹配后会停止**

​	4.1 基本分支结构

```js
var regex = /good|nice/g;
var string = "good idea, nice try.";
console.log( string.match(regex) ); 
// => ["good", "nice"]
```

​	4.2 特性一：惰性匹配，前面的优先级更高

```js
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

​	4.3 特性二：管道符号永远优先级最低

*这里简单提一下后面会详细说*

```js
// /acc|b*/ !== /(acc|b)*/

// /acc|b*/ === /acc|(b*)/
```

### 位置匹配

| 字符  | 描述                                       |
| ----- | ------------------------------------------ |
| ^     | 匹配字符串开头                             |
| $     | 匹配字符串结尾                             |
| \b    | 字符边界（\w与\W, \w与^, \w与$之间的位置） |
| \B    | 除了上面的位置外的所有位置                 |
| (?=p) | p前面的位置                                |
| (?!p) | 除了p前面外的所有位置                      |

首先，位置的概念，在正则中位置的概念都可以理解为每个字符的中间都有一个空字符连接，而位置匹配就是匹配中间这些位置

例：hello => h + "" + e + "" + l + "" + l + "" + o

其中后面四个都很好理解，不会有太多复杂的应用

```js
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

```js
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

- 应用一：匹配全字符（匹配完整字符）

```js
var regex = /^hello$/
```

- 应用二：逆向匹配（确保字符串中未达成某条件）

```js
// 不可以全部是数字
var regex = /(?!^[0-9]*$)^/
regex.test('1111')
// true
regex.test('111r')
// false
```

