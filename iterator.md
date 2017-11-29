# es6笔记—iterator接口

具备原生iterator接口的数据类型
1. String
2. Array
3. Map
4. Set
5. arguments对象
6. nodeList对象
7. TypedArray


### 什么是Iterator（遍历器）

是一种统一的接口机制，为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署了Iterator接口，就可以完成遍历操作。

- **作用**：
  1. 为各种数据结构提供统一的接口
  2. 使数据结构的成员可以按某种次序排列
  3. 创造了新的遍历命令(let...of...)

- **遍历过程**
  1. 创建一个指针对象，指向数据的成员
  2. 第一次调用指针对象的`next`方法，指针指向数据的第一个成员
  3. 第二次调用的指针对象的`next`方法，指针指向数据的第二个成员
  4. 不断调用指针对象的`next`方法，直到指向数据结构的结束位置

  上代码模拟针对对象
  ```js
  var it = makeIterator(['a', 'b'])
  
  it.next() // {value: "a", done: false}
  it.next() // {value: "b", done: false}
  it.next() // {value: undefined, done: true}
  
  function makeIterator(array) {
    var nextIndex = 0;
    // 返回遍历器对象
    return {
        // next方法用于移动指针
        next: function() {
            return nextIndex > array.length ?
              {value: array[nextIndex++], done: false} :
              {value: undefined, done: true}
        }
    }
  }
  ```
  > `markIterator`函数是一个遍历器生成函数，**返回**的是一个**遍历器对象**
  
  如果使用TypeScript的写法，关于`Iterable`遍历器接口，`Iterator`遍历（指针）对象和`next`方法
  ```TypeScript
  interface Iterable {
      [Symbol.iterator](): Iterator
  }
  
  interface Iterator {
      next(value?: any) : IterationResult
  }
  
  interface IterationResult {
      value : any,
      done : boolean
  }
  ```
  
### Iterator使用场合

1. 对数组和Set解构赋值会默认调用`Symbol.iterator`方法

2. 扩展运算符会默认调用Iterator接口

    ```js
    let arr = ['b', 'c']
    ['a', ...arr, 'd']  // ['a', 'b', 'c', 'd']
    ```
    
    > 任何数据结构，只要部署了Iterator接口，就可以使用扩展运算符将其转化
    
    ```js
    let arr = [...iterable];
    ```
    
3. 其他
    - for...of...
    - Array.from()
    - Map(), Set(), WeakMap(), WeakSet()
    - Promise.all
    - Promise.race()


### `for...of`  vs  `for...in...`

1. for...in... 会遍历原型链上的属性
2. 遍历数组的时候, for...in...也会遍历手动添加的属性
