Array.prototype.sort()
> 1. [Array.prototype.sort() - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)
> 2. [js中数组(Array)的排序(sort)注意事项](http://www.cnblogs.com/yjmyzz/archive/2009/10/20/1586896.html)

`sort()` 方法对数组的元素做原地的排序，并返回这个数组。 `sort` 可能不是[稳定的](https://zh.wikipedia.org/wiki/排序算法#.E7.A9.A9.E5.AE.9A.E6.80.A7)。默认按照字符串的`UNICODE`码位点 `(code point)` 排序。

数组调用`sort()`方法后，会影响本身(而非生成新数组)

## 语法 ##
> arr.sort([compareFunction])

## 参数 ##

**compareFunction**

  可选。用来指定按某种顺序进行排列的函数。如果省略，元素按照转换为的字符串的诸个字符的万国码位点进行排序。

## 描述 ##
如果没有指明 `compareFunction` ，那么元素会被转换为字符串并按照万国码位点顺序排序。例如 `"Cherry"` 会被排列到 `"banana"` 之前。当对数字进行排序的时候， `9` 会出现在 `80` 之后，因为他们会先被转换为字符串，而 `"80"` 比 `"9"` 要靠前。

如果指明了 `compareFunction` ，那么数组会按照调用该函数的返回值排序。记 `a` 和 `b` 是两个将要被比较的元素：

- 如果 `compareFunction(a, b)` 小于 `0` ，那么 `a` 会被排列到 `b` 之前；

- 如果 `compareFunction(a, b)` 等于 `0` ， `a` 和 `b` 的相对位置不变;

- 如果 `compareFunction(a, b)` 大于 `0` ， `b` 会被排列到 `a` 之前。

## 实例 ##
### 按照数值的大小对数字进行排序 ###
希望比较数字而非字符串，比较函数可以简单的以 `b` 减 `a`，如下的函数将会将数组升序排列
```js
var numbers = [4, 2, 5, 1, 3];
numbers.sort(function(a, b) {
  return a - b;
});
console.log(numbers);

// [1, 2, 3, 4, 5]
```
### 对象可以按照某个属性排序： ###
```js
var items = [
  { name: 'Edward', value: 21 },
  { name: 'Sharpe', value: 37 },
  { name: 'And', value: 45 },
  { name: 'The', value: -12 },
  { name: 'Magnetic' },
  { name: 'Zeros', value: 37 }
];

items.sort(function (a, b) {
  if (a.value > b.value) {
    return 1;
  }
  if (a.value < b.value) {
    return -1;
  }
  // a 必须等于 b
  return 0;
});
```
## 使用映射改善排序 ##

`compareFunction` 可能需要对元素做多次映射以实现排序，尤其当 `compareFunction` 较为复杂，且元素较多的时候，某些 `compareFunction` 可能会导致很高的负载。使用 `map` 辅助排序将会是一个好主意。基本思想是首先将数组中的每个元素比较的实际值取出来，排序后再将数组恢复。
```js
// 需要被排序的数组
var list = ['Delta', 'alpha', 'CHARLIE', 'bravo'];

// 对需要排序的数字和位置的临时存储
var mapped = list.map(function(el, i) {
  return { index: i, value: el.toLowerCase() };
})

// 按照多个值排序数组
mapped.sort(function(a, b) {
  return +(a.value > b.value) || +(a.value === b.value) - 1;
});

// 根据索引得到排序的结果
var result = mapped.map(function(el){
  return list[el.index];
});
```


