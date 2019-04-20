### 数组去重
##### 基础版
```JavaScript
const uniq = (arr = []) => {
    const tem = Object.create(null) // 创建一个纯净的对象
    const result = []
    const len = arr.length
    for (let i = 0; i < len; i++) {
        if (!tem[arr[i]]) {
            tem[arr[i]] = true
            result.push(arr[i])
        }
    }
    return result
}

const params = [1, 1, 2, 2, 3, 3, 4]

uniq(params) // [1, 2, 3, 4]
```

##### ES6增强版 
```JavaScript
// 通过 Array.from
const params = [1, 1, 2, 2, 3, 3, 4]
Array.from(new Set(params)) // [1, 2, 3, 4]

// 通过 ... 拓展运算符
[...new Set(params)] // [1, 2, 3, 4]
```

### 数组扁平化
##### 基础版
```JavaScript
const isType = type => value => (({}).toString.call(value) === `[object ${type}]`)
const isArray = isType('Array')
// 等同与 Array.isArray

/**
 * pre.concat(isArray(cur) ? flatten(cur) : cur)
 * 使用 ...拓展运算符 等同于
 * [...pre, ...isArray(cur) ? flatten(cur) : cur]
*/
const flatten = (arr = []) => (
    arr.reduce((pre, cur) => (
        pre.concat(isArray(cur) ? flatten(cur) : cur)), []
    )
)

const params = [1, [2, 2, [3, 3], [4]]]

flatten(params) // [1, 2, 2, 3, 3, 4]
```

##### ES6增强版 
```JavaScript
const params = [1, [2, 2, [3, 3], [4]]]
Array.flat(params) // [1, 2, 2, 3, 3, 4]
```

### 类数组的转换
##### 基础版
```JavaScript
const params = { 0: 0, 1: 1, length: 2 }

Array.prototype.slice.call(params) // [0, 1]
Array.prototype.slice.apply(params) // [0, 1]
```

##### ES6增强版 
```JavaScript
const params = { 0: 0, 1: 1, length: 2 }

Array.from(params) // [0, 1]
[...parans] // [0, 1]
```

