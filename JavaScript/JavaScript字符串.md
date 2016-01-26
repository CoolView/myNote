# 字符串 #
字符串常见的操作如下：
```js
var s = 'Hello, world!';
s.length; // 13
```
要获取字符串某个指定位置的字符，使用类似`Array`的下标操作，索引号从`0`开始：
```js
var s = 'Hello, world!';

s[0]; // 'H'
s[6]; // ' '
s[13]; // undefined 超出范围的索引不会报错，但一律返回undefined
```
需要特别注意的是，字符串是不可变的，如果对字符串的某个索引赋值，不会有任何错误，但是，也没有任何效果：
```js
var s = 'Test';
s[0] = 'X';
alert(s); // s仍然为'Test'
```
## 字符串比较 ##
```js
var a = "a";
var b = "b";
if (a < b) // true
  print(a + " is less than " + b);
else if (a > b)
  print(a + " is greater than " + b);
else
  print(a + " and " + b + " are equal.");
```
## 常用方法 ##
`JavaScript`为字符串提供了一些常用方法，注意，调用这些方法本身不会改变原有字符串的内容，而是返回一个新字符串：
### toUpperCase ###
`toUpperCase()`把一个字符串全部变为大写：
```js
var s = 'Hello';
s.toUpperCase(); // 返回'HELLO'
```
### toLowerCase ###
`toLowerCase()`把一个字符串全部变为小写：
```js
var s = 'Hello';
var lower = s.toLowerCase(); // 返回'hello'并赋值给变量lower
lower; // 'hello'
```
### indexOf ###
`indexOf()`会搜索指定字符串出现的位置：
#### 语法 ####
> stringObject.indexOf(searchvalue,fromindex)

#### 参数	描述 ####
```
searchvalue	
	必需。规定需检索的字符串值。
fromindex	
	可选的整数参数。规定在字符串中开始检索的位置。
	它的合法取值是 0 到 stringObject.length - 1。如省略该参数，则将从字符串的首字符开始检索。
```
#### 提示和注释 ####
***注释：***`lastIndexOf()`, `indexOf()` 方法对大小写敏感！
注释：如果要检索的字符串值没有出现，则该方法返回 `-1`。
#### 实例 ####
```js
var s = 'hello, world';
s.indexOf('world'); // 返回7
s.indexOf('World'); // 没有找到指定的子串，返回-1
```
### lastIndexOf ###
`lastIndexOf()` 方法可返回一个指定的字符串值最后出现的位置，在一个字符串中的指定位置从后向前搜索。
用法同 `indexOf()`
### charAt ###
`charAt()` 方法可返回指定位置的字符。
#### 提示和注释 ####
注释：字符串中第一个字符的下标是 `0`。如果参数 `index` 不在 `0` 与 `string.length` 之间，该方法将返回一个`空字符串`。
#### 实例 ####
在字符串 `"Hello world!"` 中，我们将返回位置 `1` 的字符：
```html
<script type="text/javascript">

var str="Hello world!"
document.write(str.charAt(1))

</script>
```
以上代码的输出是：
> e

### charCodeAt ###
`charCodeAt()` 方法可返回指定位置的字符的 `Unicode` 编码。这个返回值是 `0 - 65535` 之间的整数。
方法 `charCodeAt()` 与 `charAt()` 方法执行的操作相似，只不过前者返回的是位于指定位置的字符的编码，而后者返回的是字符子串。
#### 提示和注释 ####
注释：字符串中第一个字符的下标是 `0`。如果 `index` 是负数，或大于等于字符串的长度，则 `charCodeAt(`) 返回 **`NaN`**。
#### 实例 ####
在字符串 `"Hello world!"` 中，我们将返回位置 `1` 的字符的 `Unicode` 编码：
```html
<script type="text/javascript">

var str="Hello world!"
document.write(str.charCodeAt(1))

</script>
```
以上代码的输出是：
> 101

### fromCharCode ###
`fromCharCode()` 可接受一个指定的 `Unicode` 值，然后返回一个字符串。
#### 语法 ####
> String.fromCharCode(numX,numX,...,numX)

#### 参数	描述 ####
> numX	必需。一个或多个 Unicode 值，即要创建的字符串中的字符的 Unicode 编码。

#### 提示和注释 ####
注释：该方法是 `String` 的***静态方法***，字符串中的每个字符都由单独的数字 `Unicode` 编码指定。
它不能作为您已创建的 `String` 对象的方法来使用。因此它的语法应该是 `String.fromCharCode()`，而不是 `myStringObject.fromCharCode()`。
#### 实例 ####
在本例中，我们将根据 `Unicode` 来输出 `"HELLO"` 和 `"ABC"`：
```html
<script type="text/javascript">

document.write(String.fromCharCode(72,69,76,76,79))
document.write("<br />")
document.write(String.fromCharCode(65,66,67))

</script>
```
以上代码的输出：
> HELLO
> ABC

### match ###
`match()` 方法可在字符串内检索指定的值，或找到一个或多个正则表达式的匹配。
该方法类似 `indexOf()` 和 `lastIndexOf()`，但是它返回指定的值，而不是字符串的位置。
#### 语法 ####
> stringObject.match(searchvalue)
> stringObject.match(regexp)

#### 参数	描述 ####
```
searchvalue	
	必需。规定要检索的字符串值。
regexp	
	必需。规定要匹配的模式的 RegExp 对象。
	如果该参数不是 RegExp 对象，则需要首先把它传递给 RegExp 构造函数，将其转换为 RegExp 对象。
```
#### 返回值 ####
存放匹配结果的数组。该数组的内容依赖于 `regexp` 是否具有全局标志 `g`。
### 说明 ###
`match()` 方法将检索字符串 `stringObject`，以找到一个或多个与 `regexp` 匹配的文本。这个方法的行为在很大程度上有赖于 `regexp` **是否具有标志 `g`**。

如果 `regexp` 没有标志 `g`，那么 `match()` 方法就只能在 `stringObjec`t 中执行*一次匹配*。如果没有找到任何匹配的文本， `match()` 将返回 `null`。否则，它将返回一个数组，其中存放了与它找到的匹配文本有关的信息。该数组的第 `0` 个元素存放的是匹配文本，而其余的元素存放的是与正则表达式的子表达式匹配的文本。除了这些常规的数组元素之外，返回的数组还含有**两个对象属性**。`index` 属性声明的是匹配文本的起始字符在 `stringObject` 中的位置，`input` 属性声明的是对 `stringObject` 的引用。

如果 `regexp` 具有标志 `g`，则 `match()` 方法将执行全局检索，找到 `stringObject` 中的所有匹配子字符串。若没有找到任何匹配的子串，则返回 `null`。如果找到了一个或多个匹配子串，则返回一个数组。不过全局匹配返回的数组的内容与前者大不相同，它的数组元素中存放的是 `stringObject` 中所有的匹配子串，而且也没有 `index` 属性或 `input` 属性。
注意：在全局检索模式下，`match()` 即不提供与子表达式匹配的文本的信息，也不声明每个匹配子串的位置。如果您需要这些全局检索的信息，可以使用 `RegExp.exec()`。
#### 例子 1 ####
在本例中，我们将在 "Hello world!" 中进行不同的检索：
```html
<script type="text/javascript">

var str="Hello world!"
document.write(str.match("world") + "<br />")
document.write(str.match("World") + "<br />")
document.write(str.match("worlld") + "<br />")
document.write(str.match("world!"))

</script>
```
输出：
> world
> null
> null
> world!

#### 例子 2 ####
在本例中，我们将使用全局匹配的正则表达式来检索字符串中的所有数字：
```html
<script type="text/javascript">

var str="1 plus 2 equal 3"
document.write(str.match(/\d+/g))
var s = str.match(/\d+/);
document.write(s.input);
document.write(s);
document.write(s.index);
</script>
```
输出：
> 1,2,3
> 1 plus 2 equal 3
> 1
> 0

### replace ###
`replace()` 方法用于在字符串中用一些字符替换另一些字符，或替换一个与正则表达式匹配的子串。
#### 语法 ####
> stringObject.replace(regexp/substr,replacement)

#### 参数	描述 ####
```
regexp/substr	
	必需。规定子字符串或要替换的模式的 RegExp 对象。
	请注意，如果该值是一个字符串，则将它作为要检索的直接量文本模式，而不是首先被转换为 RegExp 对象。
replacement	
	必需。一个字符串值。规定了替换文本或生成替换文本的函数。
```
#### 返回值 ####
一个新的字符串，是用 replacement 替换了 regexp 的第一次匹配或所有匹配之后得到的。
#### 说明 ####
字符串 `stringObject` 的 `replace()` 方法执行的是查找并替换的操作。它将在 `stringObject` 中查找与 `regexp` 相匹配的子字符串，然后用 `replacement` 来替换这些子串。如果 `regexp` 具有全局标志 `g`，那么 `replace(`) 方法将替换所有匹配的子串。否则，它只替换第一个匹配子串。

`replacement` 可以是字符串，也可以是函数。如果它是字符串，那么每个匹配都将由字符串替换。但是 `replacement` 中的 `$` 字符具有特定的含义。如下表所示，它说明从模式匹配得到的字符串将用于替换。

| 字符 |	替换文本 |
| :--------:   | :-----:  	| 
| $1、$2、...、$99	| 与 regexp 中的第 1 到第 99 个子表达式相匹配的文本。|
| $&	| 与 regexp 相匹配的子串。|
| $`	| 位于匹配子串左侧的文本。|
| $'	| 位于匹配子串右侧的文本。|
| $$	| 直接量符号。|
注意：`ECMAScript v3` 规定，`replace()` 方法的参数 `replacement` 可以是函数而不是字符串。在这种情况下，每个匹配都调用该函数，它返回的字符串将作为替换文本使用。该函数的第一个参数是匹配模式的字符串。接下来的参数是与模式中的子表达式匹配的字符串，可以有 `0` 个或多个这样的参数。接下来的参数是一个整数，声明了匹配在 `stringObject` 中出现的位置。最后一个参数是 `stringObject` 本身。

#### 例子 1 ####
在本例中，我们将使用 `"W3School"` 替换字符串中的 `"Microsoft"`：
```html
<script type="text/javascript">

var str="Visit Microsoft!"
document.write(str.replace(/Microsoft/, "W3School"))

</script>
```
输出：
> Visit W3School!

#### 例子 2 ####
在本例中，我们将执行一次全局替换，每当 `"Microsoft"` 被找到，它就被替换为 `"W3School"`：
```html
<script type="text/javascript">

var str="Welcome to Microsoft! "
str=str + "We are proud to announce that Microsoft has "
str=str + "one of the largest Web Developers sites in the world."

document.write(str.replace(/Microsoft/g, "W3School"))

</script>
```
输出：
> Welcome to W3School! We are proud to announce that W3School has one of the largest Web Developers sites in the world.

#### 例子 3 ####
您可以使用本例提供的代码来确保匹配字符串大写字符的正确：
```js
text = "javascript Tutorial";
text.replace(/javascript/i, "JavaScript");
```
#### 例子 4 ####
在本例中，我们将把 `"Doe, John"` 转换为 `"John Doe"` 的形式：
```js
name = "Doe, John";
name.replace(/(\w+)\s*, \s*(\w+)/, "$2 $1");
```
#### 例子 5 ####
在本例中，我们将把所有的双引号替换为单引号：
```js
name = '"a", "b"';
name.replace(/"([^"]*)"/g, "'$1'");
name.replace(/\"/g, "'");
```
#### 例子 6 ####
在本例中，我们将把字符串中所有单词的首字母都转换为大写：
```js
name = 'aaa bbb ccc';
uw=name.replace(/\b\w+\b/g, function(word){
  return word.substring(0,1).toUpperCase()+word.substring(1);}
  );
```
### search ###
`search()` 方法用于检索字符串中指定的子字符串，或检索与正则表达式相匹配的子字符串。
#### 语法 ####
> stringObject.search(regexp)

#### 参数	描述 ####
```
regexp	
	该参数可以是需要在 stringObject 中检索的子串，也可以是需要检索的 RegExp 对象。
注释：要执行忽略大小写的检索，请追加标志 i。
注释：如果没有找到任何匹配的子串，则返回 -1。
```
#### 返回值 ####
> stringObject 中第一个与 regexp 相匹配的子串的起始位置。

#### 说明 ####
`search()` 方法不执行全局匹配，它将忽略标志 `g`。它同时忽略 `regexp` 的 `lastIndex` 属性，并且总是从字符串的开始进行检索，这意味着它总是返回 `stringObject` 的**第一个匹配的位置**。

#### 例子 1 ####
在本例中，我们将检索 `"W3School"`：
```html
<script type="text/javascript">

var str="Visit W3School!"
document.write(str.search(/W3School/))

</script>
```
输出：
> 6

在下面的例子中，无法检索到 `w3school`（因为 `search()` 对大小写敏感）。
```html
<script type="text/javascript">

var str="Visit W3School!"
document.write(str.search(/w3school/))

</script>
```
输出：
> -1

#### 例子 2 ####
在本例中，我们将执行一次忽略大小写的检索：
```html
<script type="text/javascript">

var str="Visit W3School!"
document.write(str.search(/w3school/i))

</script>
```
输出：
> 6

### split ###
`split()` 方法用于把一个字符串分割成字符串数组。
#### 语法 ####
> stringObject.split(separator,howmany)

#### 参数	描述 ####
```
separator	
	必需。字符串或正则表达式，从该参数指定的地方分割 stringObject。
howmany	
	可选。该参数可指定返回的数组的最大长度。如果设置了该参数，返回的子串不会多于这个参数指定的数组。
	如果没有设置该参数，整个字符串都会被分割，不考虑它的长度。

注释：如果把空字符串 ("") 用作 separator，那么 stringObject 中的每个字符之间都会被分割。
注释：String.split() 执行的操作与 Array.join 执行的操作是相反的。
```
#### 例子 1 ####
在本例中，我们将按照不同的方式来分割字符串：
```html
<script type="text/javascript">

var str="How are you doing today?"

document.write(str.split(" ") + "<br />")
document.write(str.split("") + "<br />")
document.write(str.split(" ",3))

</script>
```
输出：
> How,are,you,doing,today?
> H,o,w, ,a,r,e, ,y,o,u, ,d,o,i,n,g, ,t,o,d,a,y,?
> How,are,you

#### 例子 2 ####
在本例中，我们将分割结构更为复杂的字符串：
```js
"2:3:4:5".split(":")	//将返回["2", "3", "4", "5"]
"|a|b|c".split("|")	//将返回["", "a", "b", "c"]
```
#### 例子 3 ####
使用下面的代码，可以把句子分割成单词：
```js
var words = sentence.split(' ')
```
或者使用正则表达式作为 `separator`：
```js
var words = sentence.split(/\s+/)
```
#### 例子 4 ####
如果您希望把单词分割为字母，或者把字符串分割为字符，可使用下面的代码：
```js
"hello".split("")	//可返回 ["h", "e", "l", "l", "o"]
```
若只需要返回一部分字符，请使用 howmany 参数：
```js
"hello".split("", 3)	//可返回 ["h", "e", "l"]
```
### slice ###
`slice()` 方法可提取字符串的某个部分，并以新的字符串返回被提取的部分。
#### 语法 ####
> stringObject.slice(start,end)

#### 参数	描述 ####
```
start	
	要抽取的片断的起始下标。如果是负数，则该参数规定的是从字符串的尾部开始算起的位置。
	也就是说，-1 指字符串的最后一个字符，-2 指倒数第二个字符，以此类推。
end	
	紧接着要抽取的片段的结尾的下标。若未指定此参数，则要提取的子串包括 start 到原字符串结尾的字符串。
	如果该参数是负数，那么它规定的是从字符串的尾部开始算起的位置。
```

#### 说明 ####
`String` 对象的方法 `slice()`、`substring()` 和 `substr()` （不建议使用）都可返回字符串的指定部分。`slice()` 比 `substring()` 要灵活一些，因为它允许使用负数作为参数。`slice()` 与 `substr()` 有所不同，因为它用两个字符的位置来指定子串，而 `substr()` 则用字符位置和长度来指定子串。
还要注意的是，`String.slice()` 与 `Array.slice()` 相似。

#### 例子 1 ####
在本例中，我们将提取从位置 6 开始的所有字符：
```html
<script type="text/javascript">

var str="Hello happy world!"
document.write(str.slice(6))

</script>
```
输出：
> happy world!

#### 例子 2 ####
在本例中，我们将提取从位置 6 到位置 11 的所有字符：
```html
<script type="text/javascript">

var str="Hello happy world!"
document.write(str.slice(6,11))

</script>
```
输出：
> happy

### substring ###

`substring()`返回指定索引区间的子串：
```js
var s = 'hello, world'
s.substring(0, 5); // 从索引0开始到5（不包括5），返回'hello'
s.substring(7); // 从索引7开始到结束，返回'world'
```



### anchor ###
`anchor()`方法用于创建 `HTML` 锚。
```
stringObject.anchor(anchorname)
```
#### 参数	描述 ####
> anchorname	必需。为锚定义名称。

#### 实例 ####
在本例中，我们会为文本添加一个锚：
```html
<script type="text/javascript">

var txt="Hello world!"
document.write(txt.anchor("myanchor"))

</script>
```
上面的代码将输出为纯粹的 `HTML`：
```html
<a name="myanchor">Hello world!</a>
```