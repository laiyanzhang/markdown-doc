# JS方法原生实现

标签（空格分隔）： JavaScript

---

## 1.防抖
```javascript
function debounce(fn, delay) {
  let timer
  return function (...args) {
    if (timer) {
      clearTimeout(timer)
    }
    timer = setTimeout(() => {
      fn.apply(this, args)
    }, delay)
  }
}

```

## 2.节流
```javascript
function throttle(fn, delay) {
  let last = 0 // 上次触发时间
  return (...args) => {
    const now = Date.now()
    if (now - last > delay) {
      last = now
      fn.apply(this, args)
    }
  }
}

```

## 3.深拷贝
```javascript
function deepClone(obj) {
    if (!obj || typeof obj !== 'object') return obj  //对象不存在或为基本类型return 
    if (obj instanceof Date) return new Date(obj)  // Date类型
    if (obj instanceof RegExp) return new RegExp(obj)  // RegExp类型
  
    let i,ret
    // Array 类型
    if (obj instanceof Array) {
        ret = []
        for (i in obj) {
            ret.push(deepClone(obj[i]))
        }
    }
    if (obj instanceof Object) {
        ret = {}
        for (i in obj) {
           ret[i] = deepClone(obj[i]) 
        }
    }
    return ret
}

```

## 4.Promise
```javascript
class MyPromise {
  constructor(executor) { // executor执行器
    this.status = 'pending' // 等待状态
    this.value = null // 成功或失败的参数
    this.fulfilledCallbacks = [] // 成功的函数队列
    this.rejectedCallbacks = [] // 失败的函数队列
    const that = this
    function resolve(value) { // 成功的方法
      if (that.status === 'pending') {
        that.status = 'resolved'
        that.value = value
        that.fulfilledCallbacks.forEach(myFn => myFn(that.value)) //执行回调方法
      }
    }
    function reject(value) { //失败的方法
      if (that.status === 'pending') {
        that.status = 'rejected'
        that.value = value
        that.rejectedCallbacks.forEach(myFn => myFn(that.value)) //执行回调方法
      }
    }
    try {
      executor(resolve, reject)
    } catch (err) {
      reject(err)
    }
  }
  then(onFulfilled, onRejected) {
    if (this.status === 'pending') {
      // 等待状态，添加回调函数到成功的函数队列
      this.fulfilledCallbacks.push(() => {
        onFulfilled(this.value)
      })
      // 等待状态，添加回调函数到失败的函数队列
      this.rejectedCallbacks.push(() => {
        onRejected(this.value)
      })
    }
    if (this.status === 'resolved') { // 支持同步调用
      onFulfilled(this.value)
    }
    if (this.status === 'rejected') { // 支持同步调用
      onRejected(this.value)
    }
  }
  all(promises) {
    let results = [];
    let promiseCount = 0;
    let promisesLength = promises.length;
    return new Promise(function(resolve, reject) {
      for (let val of promises) {
        Promise.resolve(val).then(function(res) {
          promiseCount++
          results.push(res)
          // 当所有函数都正确执行了，resolve输出所有返回结果。
          if (promiseCount === promisesLength) {
              return resolve(results)
            }
        }, function(err) {
          return reject(err)
        })
      }
    })
  }
}

```


## 5.获取url参数
```javascript
// 创建一个URLSearchParams实例
const urlSearchParams = new URLSearchParams(window.location.search);
// 把键值对列表转换为一个对象
const params = Object.fromEntries(urlSearchParams.entries());
```


## 6.事件总线|发布订阅
```javascript
class EventEmitter {
  constructor() {
    this.cache = {}
  }

  on(name, fn) {
      this.cache[name] = fn
  }

  off(name) {
    const task = this.cache[name]
    if(task) {
       delete this.cache[name]
    }
  }
  
  emit(name, ...args) {
    let handler = this.cache[name]
    if (args.length > 0) {
       handler.apply(this, args);
    } else {
       handler.call(this);
    }
  }
}
```


## 7.new
```javascript
function myNew () {
	//创建一个新对象
	var obj = {};
	//取出参数中的第一个参数，获得构造函数
	var constructor = Array.prototype.shift.call(arguments);
	//连接原型，新对象可以访问原型中的属性
	obj._proto_ = constructor.prototype;
	// 执行构造函数，即绑定 this，并且为这个新对象添加属性
	var result = constructor.apply(obj,arguments);
	return typeof result === "object" ? result :obj ;
}
```


## 8.call
```javascript
Function.prototype.myCall = function(context) {
  context.fn = this;
  let args = [];
  if(arguments.length > 1) {
     for (let i = 1; i < arguments.length; i++) {
       args.push(arguments[i]);
     }
  }
  let result = context.fn(...args);
  delete context.fn;
  return result;
};

```


## 9.apply
```javascript
Function.prototype.myapply = function(context) {
  context.fn = this;
  let args = [];
  if(arguments[1] && arguments[1] instanceof Array) {
     args = arguments[1]
  }
  let result = context.fn(...args);
  delete context.fn;
  return result;
};
```


## 10.bind
```javascript
Function.prototype.bind = function () {
    var self = this,                        // 保存原函数
    context = [].shift.call(arguments), // 保存需要绑定的this上下文
    args = [].slice.call(arguments, 0);    // 剩余的参数转为数组
    return function () {                    // 返回一个新函数
        self.apply(context, args.call([].slice.call(arguments, 0)));
    }
}
```

## 11.reduce
```javascript
Array.prototype.fakeReduce = function fakeReduce(fn, base) {

  // 获取数组及其副本
  let initialArr = this;
  let arr = initialArr.concat();

  // 有初始值时加入数组内部进行计算
  if (base) arr.unshift(base);
  let index, newValue;
 
  // 进行归并处理
  while (arr.length > 1) {
    index = initialArr.length - arr.length + 1;//计算当前副本的索引值
    newValue = fn.call(null, arr[0], arr[1], index, initialArr);//传入数值进行处理

    arr.splice(0, 2, newValue); // 直接用 splice 实现替换
  }

  return newValue;
};
```

## 12.排序算法
### 1.时间复杂度
- n*n：冒泡排序、选择排序、插入排序
- nlogn：快速排序、归并排序、堆排序

### 2.冒泡排序
- 定义：每次都是相邻元素比较，第一个元素比第二个元素大则交换位置直到最后一个元素为最大，继续循环
- 代码实现：
``` javascript
function bubbleSort(arr) {
    var len = arr.length;
    for (var i = 0; i < len - 1; i++) {
        for (var j = 0; j < len - 1 - i; j++) {
            if (arr[j] > arr[j+1]) {        // 相邻元素两两对比
                var temp = arr[j+1];        // 元素交换
                arr[j+1] = arr[j];
                arr[j] = temp;
            }
        }
    }
    return arr;
}
```

### 3.选择排序
- 定义：遍历未排序数组找出最小元素放到未排序数组的起始位置
- 代码实现：
``` javascript
function selectionSort(arr) {
    var len = arr.length;
    var minIndex, temp;
    for (var i = 0; i < len - 1; i++) {
        minIndex = i;
        for (var j = i + 1; j < len; j++) {
            if (arr[j] < arr[minIndex]) {     // 寻找最小的数
                minIndex = j;                 // 将最小数的索引保存
            }
        }
        temp = arr[i];
        arr[i] = arr[minIndex];
        arr[minIndex] = temp;
    }
    return arr;
}
```

### 4.插入排序
- 定义：遍历未排序数组，将未排序元素与排序数组逐个比较，插入到对应位置
- 代码实现：
``` javascript
function insertionSort(arr) {
    var len = arr.length;
    var preIndex, current;
    for (var i = 1; i < len; i++) {
        preIndex = i - 1;
        current = arr[i];
        while (preIndex >= 0 && arr[preIndex] > current) {
            arr[preIndex + 1] = arr[preIndex];    // 数组往前移
            preIndex--;                           // 获取数组下标
        }
        arr[preIndex + 1] = current;
    }
    return arr;
}
```


### 5.归并排序
- 定义：利用递归的思想，数组不断拆分成小数组，小数组内部排序完成返回有序数组，根据返回结果递归形成完整有序数组
- 代码实现：
``` javascript
function mergeSort(arr) {
    var len = arr.length;
    if (len < 2) {
        return arr;
    }
    var middle = Math.floor(len / 2),
        left = arr.slice(0, middle),
        right = arr.slice(middle);
    return merge(mergeSort(left), mergeSort(right));
}
 
function merge(left, right) {
    var result = [];
 
    while (left.length>0 && right.length>0) {
        if (left[0] <= right[0]) {
            result.push(left.shift());
        } else {
            result.push(right.shift());
        }
    }
 
    while (left.length)
        result.push(left.shift());
 
    while (right.length)
        result.push(right.shift());
 
    return result;
}
```


### 6.快速排序
- 定义：
  - 将第一个元素作为基准值，遍历数组，比基准小的放在左边，最后将第一个元素与最后一个左边元素调换位置
  - 以此类推，在左边以及右边的数组中重复该操作直至全部数组排序完成
- 代码实现：
``` javascript
function quickSort(arr, left, right) {
    var len = arr.length,
        partitionIndex,
        left = typeof left != 'number' ? 0 : left,
        right = typeof right != 'number' ? len - 1 : right;
 
    if (left < right) {
        partitionIndex = partition(arr, left, right);
        quickSort(arr, left, partitionIndex-1);
        quickSort(arr, partitionIndex+1, right);
    }
    return arr;
}
 
function partition(arr, left ,right) {     // 分区操作
    var pivot = left,                      // 设定基准值（pivot）
        index = pivot + 1;
    for (var i = index; i <= right; i++) {
        if (arr[i] < arr[pivot]) {
            swap(arr, i, index);
            index++;
        }       
    }
    swap(arr, pivot, index - 1);
    return index-1;
}
 
function swap(arr, i, j) {
    var temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
}
```




