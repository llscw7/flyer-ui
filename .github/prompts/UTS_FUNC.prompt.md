# UTS 函数 (Function) 开发指南

基于 UTS 官方文档的函数完整使用指南，涵盖函数定义、调用、作用域、闭包等核心特性。

## 🔧 函数定义方式

### 1. 函数声明 (Function Declaration)
```uts
// 基本函数声明 - 必须明确标明返回值类型
function add(x: string, y: string): string {
    let z: string = x + " " + y
    return z
}

// 调用函数
let result: string = add("hello", "world")
console.log(result) // "hello world"
```

### 2. 函数表达式 (Function Expression)
```uts
// 匿名函数表达式
const add = function (x: string, y: string): string {
    return x + " " + y
}

// 函数表达式可以作为值传递
const fn2 = add // ✅ 正确，函数表达式可作为值传递
```

### 3. 箭头函数 (Arrow Function)
```uts
// 箭头函数总是匿名的，语法更简洁
const arr = ["Hydrogen", "Helium", "Lithium", "Beryllium"]

// 传统函数表达式
const a2 = arr.map(function (s): number {
    return s.length
})

// 箭头函数等价写法
const a3 = arr.map((s): number => s.length)

console.log(a2) // [8, 6, 7, 9]
console.log(a3) // [8, 6, 7, 9]
```

### 4. 无返回值函数 (void)
```uts
// 可省略返回值类型或显式声明 void
function logMessage(x: string, y: string): void {
    let z: string = x + " " + y
    console.log(z)
    // return 语句可省略
}

// 或者省略返回值类型
function logMessage2(x: string, y: string) {
    console.log(x + " " + y)
}
```

### 5. 异步函数 (Async Function)
```uts
// 异步函数返回 Promise 对象
async function fetchData(): Promise<string> {
    // 异步操作
    return "data"
}

// 使用异步函数
fetchData().then(function(data) {
    console.log('获取到数据:', data)
})

// 使用 await 调用异步函数
async function handleData(): Promise<void> {
    try {
        const data = await fetchData()
        console.log(data)
    } catch (error) {
        console.log('错误:', error)
    }
}

// async 函数隐式包装返回值为 Promise
async function getNumber(): Promise<number> {
    return 1 // 自动包装为 Promise<number>
}
```

## 📋 函数参数特性

### 1. 默认参数
```uts
// 函数参数可设默认值，使参数成为可选参数
function multiply(a: number, b: number = 1): number {
    return a * b
}

multiply(5) // a=5, b=1(默认), 结果=5
multiply(5, 3) // a=5, b=3, 结果=15

// 默认值可以为 null
class Person {
    test(msg: string | null = null) {
        if (msg != null) {
            console.log(msg)
        }
    }
}

// ⚠️ 限制：函数表达式不支持默认参数
const testFn = function(msg: string | null) { 
    // 不能设置默认参数
}
```

### 2. 剩余参数 (Rest Parameters)
```uts
// 使用 ...操作符定义最后一个参数为剩余参数
function logMessages(prefix: string, ...messages: string[]) {
    console.log(prefix, messages)
}

// 调用方式
logMessages('日志:') // '日志:' []
logMessages('日志:', 'msg1', 'msg2') // '日志:' ['msg1', 'msg2']

// 使用展开语法传递数组 (Swift 平台不支持)
logMessages('日志:', ...['msg1', 'msg2']) // '日志:' ['msg1', 'msg2']

// ⚠️ 限制：uvue 页面的 methods 中不支持剩余参数 (app-android)
```

## 🎯 函数作用域与闭包

### 1. 函数作用域
```uts
const globalVar: string = "全局变量"

function outerFunction(): string {
    const localVar: string = "局部变量"
    
    // 函数内可以访问全局变量和局部变量
    return globalVar + " " + localVar
}

// ❌ 外部无法访问函数内的局部变量
// console.log(localVar) // 编译错误
```

### 2. 嵌套函数
```uts
function addSquares(a: number, b: number): number {
    // 内部函数 - 对外部函数私有
    function square(x: number): number {
        return x * x
    }
    
    // 内部函数可以访问外部函数的参数和变量
    return square(a) + square(b)
}

console.log(addSquares(2, 3)) // 13
console.log(addSquares(3, 4)) // 25
```

### 3. 命名冲突与作用域链
```uts
function outside(): (x: number) => number {
    let x = 5 // 外部函数的变量 x
    
    const inside = function (x: number): number {
        return x * 2 // 内部函数的参数 x 优先级更高
    }
    
    return inside
}

console.log(outside()(10)) // 返回 20，使用内部函数的参数 x
```

### 4. 闭包 (Closure)
```uts
// 闭包：内部函数可以访问外部函数的变量
const createPet = function (name: string): () => string {
    // 外部函数定义变量 "name"
    const getName = function (): string {
        // 内部函数可以访问外部函数的 "name"
        return name
    }
    
    // 返回内部函数，形成闭包
    return getName
}

const myPet = createPet("Vivie")
console.log(myPet()) // "Vivie"

// 更复杂的闭包示例
function createCounter(): () => number {
    let count = 0
    
    return function(): number {
        count++
        return count
    }
}

const counter = createCounter()
console.log(counter()) // 1
console.log(counter()) // 2
```

## 📝 函数类型与传递

### 1. 函数类型定义
```uts
// 函数类型表达式：(参数名：参数类型) => 返回值类型
const fn: (x: string) => void = function(x: string) {
    console.log(x)
}

// 函数类型必须严格匹配
const mathFn: (a: number, b: number) => number = function(a: number, b: number): number {
    return a + b
}

// ❌ 类型不匹配的示例
// fn = function(x: string, y: string) {} // 错误：参数个数不匹配
// fn = function(x: string): string { return x } // 错误：返回类型不匹配
```

### 2. 函数作为值传递
```uts
function regularFunction() {
    console.log("普通函数")
}

const expressionFunction = function() {
    console.log("函数表达式")
}

// ✅ 函数表达式可以作为值传递
const fn1 = expressionFunction // 正确
console.log(expressionFunction) // 正确

// ❌ 函数声明不能作为值传递
// const fn2 = regularFunction // 错误
// console.log(regularFunction) // 错误

// ✅ 匿名函数表达式可以直接使用
console.log(function() {
    return "匿名函数"
})
```

## ⚠️ UTS 函数限制与注意事项

### 1. 调用限制 - 无变量提升
```uts
// ❌ 函数表达式中不能访问未声明的变量
const fn = function () {
    console.log(fn) // 编译错误：此时 fn 还未声明
}

// ❌ 函数声明也不能访问未声明的自身
function testFn () {
    console.log(testFn) // 编译错误：此时 testFn 还未声明
}

// ✅ 正确的自引用方式
let recursiveFn: (() => void) | null = null
recursiveFn = function () {
    console.log(recursiveFn) // 此时 recursiveFn 可以正常访问
    // 递归调用
    if (recursiveFn != null) {
        // recursiveFn()
    }
}
```

### 2. 默认参数限制
```uts
// ✅ 函数声明支持默认参数
function greet(name: string = "World"): string {
    return "Hello " + name
}

// ❌ 函数表达式不支持默认参数
const greetFn = function(name: string) { // 不能设置默认值
    return "Hello " + name
}

// ❌ 对象方法不支持默认参数（会转为函数表达式）
const obj = {
    greet(name: string) { // 不能设置默认值
        return "Hello " + name
    }
}
```

### 3. 平台差异
```uts
// 异步函数在不同平台的差异
// iOS 平台和 HBuilderX 3.93 以下的 Android 平台
// 异步函数返回的不是标准 Promise 对象

// 剩余参数在 Swift 平台的限制
function spreadExample(prefix: string, ...args: string[]) {
    console.log(prefix, args)
}

// ✅ 在大多数平台可用
spreadExample('test', 'a', 'b', 'c')

// ❌ Swift 平台不支持展开语法
// spreadExample('test', ...['a', 'b', 'c'])
```

## 🚀 最佳实践

### 1. 函数定义推荐
```uts
// ✅ 推荐：明确返回类型
function calculateTotal(price: number, tax: number = 0.1): number {
    return price * (1 + tax)
}

// ✅ 推荐：复杂逻辑使用函数表达式便于传递
const complexCalculator = function(
    values: number[], 
    processor: (val: number) => number
): number[] {
    return values.map(processor)
}
```

### 2. 错误处理模式
```uts
// 同步函数错误处理
function parseNumber(input: string): number {
    const result = parseFloat(input)
    if (isNaN(result)) {
        throw new Error("无效的数字格式: " + input)
    }
    return result
}

// 异步函数错误处理
async function fetchUserData(id: string): Promise<UTSJSONObject> {
    try {
        // 模拟异步操作
        if (id.length == 0) {
            throw new Error("用户ID不能为空")
        }
        return { id: id, name: "用户" + id }
    } catch (error) {
        console.error("获取用户数据失败:", error)
        throw error
    }
}
```

### 3. 类型安全的函数设计
```uts
// 使用泛型提高复用性（如果平台支持）
function identity<T>(arg: T): T {
    return arg
}

// 联合类型参数处理
function processValue(value: string | number): string {
    if (typeof value === 'string') {
        return "字符串: " + value
    } else {
        return "数字: " + value.toString()
    }
}

// 可选参数与空值处理
function createUser(name: string, email?: string | null): UTSJSONObject {
    const user: UTSJSONObject = { name: name }
    if (email != null && email.length > 0) {
        user.email = email
    }
    return user
}
```

## 🔧 Vue3 组合式API中的函数使用

### 1. 组件中的函数定义
```uts
// 在 <script setup lang="uts"> 中的函数
import { ref, computed } from 'vue'

// 响应式函数
const count = ref(0)

function increment(): void {
    count.value++
}

// 计算属性函数
const doubleCount = computed((): number => {
    return count.value * 2
})

// 事件处理函数
function handleClick(event: any): void {
    console.log('点击事件:', event)
    increment()
}
```

### 2. 生命周期与函数
```uts
import { onMounted, onUnmounted } from 'vue'

// 清理函数
let timer: number | null = null

function startTimer(): void {
    timer = setInterval(() => {
        console.log('定时器运行中')
    }, 1000)
}

function stopTimer(): void {
    if (timer != null) {
        clearInterval(timer)
        timer = null
    }
}

onMounted(() => {
    startTimer()
})

onUnmounted(() => {
    stopTimer()
})
```

## 📋 函数开发检查清单

- [ ] 函数声明包含明确的返回类型（void 除外）
- [ ] 参数类型明确定义，避免使用 `any`
- [ ] 合理使用默认参数（仅限函数声明）
- [ ] 异步函数正确返回 `Promise` 类型
- [ ] 避免在函数表达式中访问未声明变量
- [ ] 闭包使用合理，避免内存泄漏
- [ ] 错误处理完整（try-catch 或抛出异常）
- [ ] 函数功能单一，符合单一职责原则
- [ ] 考虑平台差异（iOS/Android 特性限制）
- [ ] 在 Vue 组件中正确处理响应式数据

## 💡 常见问题解决

### Q: 函数声明与函数表达式如何选择？
**A**: 需要作为值传递时使用函数表达式；需要默认参数时使用函数声明；一般情况优先使用函数声明。

### Q: 为什么函数表达式不能访问自身？
**A**: UTS 没有变量提升，函数表达式赋值时变量尚未完成声明，可以先声明变量为 null 再赋值。

### Q: 异步函数在不同平台表现不同怎么办？
**A**: 使用平台条件编译处理差异，或统一使用 Promise 模式确保兼容性。

### Q: 剩余参数在某些平台不支持怎么办？
**A**: 可以传递数组参数替代剩余参数，或使用平台条件编译提供不同实现。
