### Set

#### 1. 定义
 - Set 本身是一个构造函数，用来生成 Set 数据结构。它类似于数组，但是 __成员的值都是唯一的，没有重复__
 - Set 接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数
 - 向 Set 加入值时不会发生类型转换，如 5 和 "5" 是两个不同的值
 - Set __存的是值的地址__，所以传入两个空对象是不相等的，因为他们不是一个地址

##### 代码示例
```javascript
const s = new Set()

let arr = [2, 3, 5, 4, 5, 2, 2]
arr.forEach(x => s.add(x))

for (let i of s) {
    console.log(i)
}
// 2 3 5 4
```

#### 2. Set 实例的属性和方法
 - 属性
  1. Set.prototype.constructor: 构造函数，默认就是 Set 函数
  2. Set.prototype.size: 返回 Set 实例的成员总数
 - 方法
  1. add(value): 添加某个值，返回 Set 结构本身
  2. delete(value): 删除某个值，返回一个布尔值，表示删除是否成功
  3. has(value): 返回一个布尔值，表示参数是否为 Set 的成员
  4. clear(): 清除所有成员，没有返回值

##### 代码示例
```javascript
const s = new Set()

s.add(1).add(2).add(2)

s.size  // 2

s.has(1)  // true
s.has(2)  // true
s.has(3)  // false

s.delete(2)
s.has(2)  // false
```

#### 3. 遍历操作
 - keys(): 返回键名
 - values(): 返回键值
 - entries(): 返回键值对
 - forEach(): 使用回调函数遍历每个成员

说明：
1. Set 的遍历顺序就是插入顺序
2. Set 结构没有键名，只有键值，所以 Set 中的 keys 和 values 两个方法效果是一样的
3. Set 遍历操作不会改变原来的 Set 结构
4. forEach 方法还可以接受第二个参数，用于绑定 this

#### 4. 应用
 - 数组去重
```javascript
let arr = [1, 2, 3, 4, 2, 3, 4]
// arr = [...new Set(arr)]  // [1, 2, 3, 4]
// arr = Array.from(new Set(arr))  // [1, 2, 3, 4]
```
 - 实现并集、交集、差集
```javascript
let a = new Set([1, 2, 3])
let b = new Set([4, 3, 2])

let union = new Set([...a, ...b])  // 并集
let intersect = new Set([...a].filter(x => b.has(x)))  // 交集
let diff = new Set([...a].filter(x => !b.has(x)))  // 差集
```


### WeakSet

#### 1. 定义
WeakSet 结构与 Set 类似，也是不重复的值的集合。   

区别：
 1. WeakSet 的成员只能是对象，而不能是其他类型的值
 2. WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用。因此，__WeakSet 不可遍历__

#### 2. WeakSet 的属性和方法
 - WeakSet 没有 size 属性
 - WeakSet 有 add(), delete(), has() 三个方法


### Map

#### 1. 定义
 - Object 提供 字符串-值 的对应，Map 提供 值-值 的对应
 - Map 接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数
 - Map 的键绑定的是内存地址，只要内存地址不一样，就视为两个键

##### 代码示例
```javascript
const m = new Map()
const o = { p: 'hello world' }

m.set(o, 'content')
m.get(o)  // 'content'
```

#### 2. Map 实例的属性和方法
 - 属性
  1. Map.prototype.size: 返回 Map 实例的成员总数
 - 方法
  1. set(key, value): 添加一个 key-value，返回 Map 结构本身
  2. get(key): 读取 key 值，如果找不到 key，则返回 undefined
  3. has(key): 返回一个布尔值，表示某个 key 是否在 Map 数据结构中
  4. delete(key): 删除某个键，返回一个布尔值，表示删除是否成功
  5. clear(): 清除所有成员，没有返回值

#### 3. 遍历操作
 - keys(): 返回键名
 - values(): 返回键值
 - entries(): 返回所有成员
 - forEach(): 遍历 Map 的所有成员

说明：
1. Map 的遍历顺序就是插入顺序
2. Set 遍历操作不会改变原来的 Set 结构
3. forEach 方法还可以接受第二个参数，用于绑定 this

##### 代码示例
```javascript
const map = new Map([['a', 1], ['b', 2]])
const reporter = {
    report: function (key, value) {
        console.log(`key: ${key}, value: ${value}`)
    }
}

map.forEach(function(value, key, map)  {
    this.report(key, value)
}, reporter)
```

### WeakMap

#### 1. 定义
 - 与 Map 类似
 - 与 WeakSet 类似

#### 2. 应用
根据 __WeakMap 的键名所指向的对象不计入垃圾回收机制__ 的特性，可以保存 __不被引用则会被自动清除的对象__ 作为键值
 - 以 DOM 节点作为键名
 - 部署私有属性

##### 代码示例
```javascript
// 监听函数放在 WeakMap 中，一旦 DOM 对象消失，与它绑定的监听函数也会自动消失
const listener = new WeakMap()

listener.set(element1, handler1)
listener.set(element2, handler2)

element1.addEventListener('click', listener.get(element1), false)
element2.addEventListener('click', listener.get(element2), false)
```


### 总结
 - Set 主要可用于 __去重__
 - Map 主要可用于 __数据存储__
 - WeakMap 主要可用于存储 __不被引用则会被自动清除__ 的对象
