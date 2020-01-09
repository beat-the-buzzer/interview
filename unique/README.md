### 数组去重的N个方法

数组去重是一个出现频率极高的面试题，这个问题很经典，有很多种解决办法。如果真的被问到了这个题或者笔试题有这道题，如果你简简单单写一个`new Set()`，并不会给你带来加分的效果。面试的时候，要去清楚地描述解决问题的思路和过程，而不仅仅是解决这个问题。

#### ES6 Set数据结构

ES6 提供了新的数据结构 Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。

这里的重复判断是在===的基础上新增了对NaN的判断。

```js
function unique (arr) {
  return [...new Set(arr)];
}
var arr = [true, true, undefined, undefined, null, null, NaN, NaN, 0, 0, 'a', 'a', {}, {}];
console.log(unique(arr));
```

对于对象类型的，arr最后两项是两个不同的字面量，不是重复的数据，如果我用下面的写法，就是另外的结果了：

```js
var obj = { a: 1 };
arr.push(obj);
arr.push(obj);
console.log(unique(arr)); // 此时数组中只有一个 {a: 1}
```

#### 循环遍历法

双重循环是一个大思路，具体的实现形式有很多种，这里只去举一个例子：

```js
function unique (arr) {
  var temp = [];
  for(var i = 0; i < arr.length; i++) {
    // 判断arr中的元素在temp中是否嫩找到，如果能找到，就不做操作，找不到的话，就把这个元素push到temp中
    if (temp.indexOf(arr[i]) === -1) {
      // 不存在的元素
      temp.push(arr[i])
    }
  }
  return temp;
}
var arr = [true, true, undefined, undefined, null, null, NaN, NaN, 0, 0, 'a', 'a', {}, {}];
console.log(unique(arr));
```

这里其实可以看做是双重循环，indexOf也算是一种循环。这里的问题就在于对NaN的判断，NaN并不等于自身，所以会返回两种NaN。

#### 利用对象的key

我们都知道，对象的Key是唯一的，利用这个特性我们可以有一个去重的思路。不过对象的key还有一个特点，就是key值必然是字符串。