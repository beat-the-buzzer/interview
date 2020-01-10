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

```js
function unique(arr) {
  var temp= [];
  var  obj = {}; // 用于存储键值
  for (var i = 0; i < arr.length; i++) {
    if (!obj[arr[i]]) {
      temp.push(arr[i]); // 不存在的key，存起来
      obj[arr[i]] = 1;
    } else {
      obj[arr[i]]++; // 记录有重复的个数
    }
  }
  return temp;
}
```

不过上面这种方式有很大的局限性，字符串'1'和数字1、true和'true'也被认为是重复的。

简单改进一下，加上类型判断，使用hasOwnProperty方法：

```js
function unique(arr) {
  var obj = {};
  return arr.filter(function(item, index) {
    return obj.hasOwnProperty(typeof item + item) ? false : (obj[typeof item + item] = true)
  })
}
function unique(arr) {
	var obj = {};
	return arr.filter(function(item, index) {
		if(obj.hasOwnProperty(typeof item + item)) {
			return false;
		} else {
			obj[typeof item + item] = true;
			return true;
		}
  })
}
```
这里把数组的值和数组的typeof值进行拼接，用计算后的值作为key，如果obj里面存了key，就return false过滤掉，没存的话，就选中了这个值，并且更新了obj对象。

这种方式的缺陷在于对对象的判断，如果数组中的元素分别是{a: 1}和{b: 2}两个对象，这两个对象计算出来的key都是object[object Object]，最后会被filter过滤掉。

在JavaScript中，如果非字符串类型的数据作为对象的key，这个值会被转成字符串类型。那么，有没有一个方法，把非字符串类型的数据作为key呢？

ES6提供了一种新的数据结构——Map，它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。

```js
function unique(arr) {
  var map = new Map();
  var array = new Array();  // 数组用于返回结果
  for (var i = 0; i < arr.length; i++) {
    if(map.has(arr[i])) {  // 如果有该key值
      map.set(arr[i], true);  // 把arr[i]作为map的key, true作为value
    } else { 
      map.set(arr[i], false);   // 如果没有该key值，就把这个key放到数组中
      array.push(arr[i]);
    }
  } 
  return array ;
}
```

用Map的方式可以解决key值去重带来的问题。