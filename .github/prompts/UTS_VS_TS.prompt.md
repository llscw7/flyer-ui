---
mode: agent
---
# UTS 与 TypeScript 核心差异指南

此文档详细说明 UTS（uni-app x 的 TypeScript 变体）与标准 TypeScript 的关键差异，以确保代码编译成功并避免运行时错误。

## 📚 基础概念差异

### 🔒 强制静态类型系统
UTS 采用强制静态类型系统，所有类型在编译时必须确定：
- **any 类型**：在 UTS 中是跨端适配的非空类型，仍需类型转换后访问具体方法
- **名义类型系统**：类型兼容性基于名称和继承关系，不支持结构化类型系统

---

## 🚫 核心语言特性约束

### 1. 禁用 undefined，强制使用 null
```uts
// ❌ TypeScript 方式
let value: string | undefined;
function test(param?: string) {
  if (param == undefined) { }
}

// ✅ UTS 方式
let value: string | null = null; // 必须初始化
function test(param: string | null) {
  if (param == null) { }
}
```

### 2. 条件语句必须使用布尔类型
```uts
// ❌ 隐式类型转换
if (1) { }
while ("") { }
const value = arr || [];

// ✅ 显式布尔判断  
if (x > 0) { }
while (isValid) { }
const value = arr != null ? arr : [];
```

### 3. 对象字面量类型推导
```uts
// ❌ TypeScript 推导为具体结构
const person = { name: "John", age: 30 };
console.log(person.name); // 直接访问

// ✅ UTS 推导为 UTSJSONObject
const person = { name: "John", age: 30 };
console.log(person["name"] as string); // 需要类型断言

// ✅ 推荐：使用 type 定义
type Person = { name: string; age: number };
const person: Person = { name: "John", age: 30 };
console.log(person.name); // 直接访问
```

### 4. 禁用变量和函数提升
```uts
// ❌ 依赖提升
console.log(x); // undefined
var x = 5;
foo(); // 可执行
function foo() {}

// ✅ 先声明后使用
let x = 5;
console.log(x);
function foo() {}
foo();
```

---

## 🔧 类型系统限制

### 1. type vs interface 使用规则
```uts
// ❌ 对象字面量不能赋值给 interface
interface Person {
  name: string;
  age: number;
}
const person: Person = { name: "John", age: 30 }; // 错误

// ✅ 只能赋值给 type
type Person = {
  name: string;
  age: number;
};
const person: Person = { name: "John", age: 30 }; // 正确
```

### 2. 禁止嵌套对象字面量
```uts
// ❌ 嵌套对象字面量
type News = {
  id: number;
  author: {
    id: number;
    name: string;
  };
};

// ✅ 提取嵌套类型
type Author = {
  id: number;
  name: string;
};
type News = {
  id: number;
  author: Author;
};
```

### 3. 禁用高级类型特性
```uts
// ❌ 不支持的类型特性
type X<T> = T extends number ? T : never; // 条件类型
type OptionsFlags<Type> = { [Property in keyof Type]: boolean }; // 映射类型
type UserUpdate = Partial<User>; // Utility Types
let x = "hello" as const; // as const 断言
let v!: T; // 确定赋值断言

// ✅ 使用基础类型和手动定义
type X = number;
type UserUpdate = { id?: number; name?: string; email?: string; };
let x: string = "hello";
let v: T = defaultValue;
```

---

## 🏗️ 类和对象约束

### 1. 类定义和使用规则
```uts
// ❌ 错误的类使用方式
class Point {
  #x: number = 0; // 私有字段语法
}
let p = new Point();
console.log(p["x"]); // 索引访问
class Child extends Parent {} // 省略构造器

// ✅ 正确的类定义
class Point {
  private x: number = 0; // private 关键字
  constructor() {}
}
let p = new Point();
console.log(p.x); // 点操作符访问（如果可见）

class Child extends Parent {
  constructor() {
    super(); // 必须显式声明构造器
  }
}
```

### 2. 接口和继承规则
```uts
// ❌ 错误的继承关系
class C { foo() {} }
class C1 implements C { foo() {} } // 类不能被 implements
interface I extends C { bar(): void; } // 接口不能继承类

// ✅ 正确的继承关系
interface C { foo(): void; }
class C1 implements C { foo() {} } // 实现接口
interface I extends BaseInterface { bar(): void; } // 接口继承接口
```

### 3. 泛型限制
```uts
// ❌ 属性方法不支持泛型
type ApiService = {
  request: <T>(url: string) => Promise<T>;
};

// ✅ 解决方案
// 方案1：提升到类型级别
type ApiService<T> = {
  request: (url: string) => Promise<T>;
};

// 方案2：定义为方法
interface ApiService {
  request<T>(url: string): Promise<T>;
}
```

---

## 🔗 函数和方法约束

### 1. 函数声明和使用
```uts
// ❌ 函数声明作为值
function foo() { console.log("foo"); }
setTimeout(foo, 1000); // 错误

// ✅ 函数表达式
const foo = () => { console.log("foo"); };
setTimeout(foo, 1000); // 正确
```

### 2. 禁用函数增强特性
```uts
// ❌ 不支持的函数特性
function greet(name: string) { /* ... */ }
greet.count = 0; // 函数属性
greet.call(context, "hello"); // call/apply/bind

// ✅ 使用类替代
class Greeter {
  count: number = 0;
  greet(name: string) { /* ... */ }
  getGreet(): (name: string) => void {
    return (name) => this.greet(name); // 闭包替代 bind
  }
}
```

---

## 🔄 类型检查和转换

### 1. 类型保护和转换
```uts
// ❌ is 运算符
function isFoo(arg: any): arg is Foo {
  return arg.foo !== undefined;
}

// ✅ instanceof 和 as
function isFoo(arg: any): boolean {
  return arg instanceof Foo;
}
function doStuff(arg: any): void {
  if (isFoo(arg)) {
    let fooArg = arg as Foo;
    console.log(fooArg.foo);
  }
}
```

### 2. 类型转换语法
```uts
// ❌ 尖括号语法
let c1 = <Circle>createShape();

// ✅ as 语法（唯一支持）
let c2 = createShape() as Circle;
```

---

## ⚡ 运算符和表达式限制

### 1. 一元运算符类型限制
```uts
// ❌ 字符串上使用一元运算符
let b = +"5"; // 错误
let d = -"5"; // 错误

// ✅ 显式类型转换
let b = parseInt("5");
let d = -parseInt("5");
```

### 2. 赋值语句限制
```uts
// ❌ 赋值语句作为表达式
y = x = 5;
while ((item = arr.pop())) { }

// ✅ 分开处理
x = 5;
y = x;
item = arr.pop();
while (item != null) {
  // ...
  item = arr.pop();
}
```

### 3. 删除操作限制
```uts
// ❌ delete 运算符
delete person.age;

// ✅ 使用 null
person.age = null;
```

---

## 📦 模块系统约束

### 1. 导入导出语法
```uts
// ❌ 不支持的语法
import m = require("mod");
export = Point;
namespace MyNamespace { /* ... */ }

// ✅ 标准 ES6 模块
import * as m from "mod";
export class Point { /* ... */ }
export { x, y };
```

---

## 🔍 特殊语言特性限制

### 1. 禁用的语言特性
```uts
// ❌ 不支持的特性
let sym = Symbol("key"); // Symbol
function* generator() { yield 1; } // 生成器
with (Math) { /* ... */ } // with 语句
let x = globalThis.abc; // globalThis

// ✅ 替代方案
let key = "uniqueKey"; // 字符串字面量
async function processItems() { /* 异步处理 */ }
let x = Math.PI; // 直接访问
import { abc } from "./module"; // 模块导入
```

### 2. 数组越界处理
```uts
// ⚠️ 平台差异警告
const arr = [1, 2, 3];
console.log(arr[5]); // Kotlin/Swift 抛异常，JS 返回 undefined

// ✅ 安全访问
const index = 5;
if (index >= 0 && index < arr.length) {
  console.log(arr[index]);
} else {
  console.log("索引越界");
}
```

---

## 🎯 开发最佳实践

### 1. 类型定义优先
```uts
// ✅ 始终明确定义类型
type Config = {
  apiUrl: string;
  timeout: number;
  retries: number;
};

type ApiResponse<T> = {
  code: number;
  data: T;
  message: string;
};
```

### 2. 安全的属性访问
```uts
// ✅ 使用安全导航和空值合并
const value = config?.timeout ?? 5000;
const result = state.resolvePromise?.(data);

// ✅ 显式空值检查
if (state.resolvePromise != null) {
  state.resolvePromise(data);
}
```

### 3. 错误处理模式
```uts
// ✅ 只抛出 Error 类型
throw new Error("操作失败");
throw new TypeError("类型错误");

// ✅ 边界检查
if (index < 0 || index >= arr.length) {
  throw new Error("数组索引越界");
}
```

### 4. 响应式状态管理
```uts
// ✅ 使用 reactive 创建响应式对象
import { reactive } from 'vue';

export const state = reactive({
  visible: false,
  data: null
} as State);

// ✅ 避免循环引用的内部函数
function resetState() {
  state.visible = false;
  state.data = null;
}

export const mutations = {
  reset() {
    resetState(); // 调用内部函数而不是自引用
  }
};
```

---

## 📋 迁移检查清单

迁移 TypeScript 代码到 UTS 时，请检查以下项目：

- [ ] 将所有 `undefined` 替换为 `null`
- [ ] 确保所有变量都初始化赋值
- [ ] 将 `interface` 用于对象字面量的改为 `type`
- [ ] 移除条件语句中的隐式布尔转换
- [ ] 提取嵌套对象字面量类型为单独的 `type`
- [ ] 将私有字段 `#field` 改为 `private field`
- [ ] 为继承类添加显式构造器
- [ ] 将函数声明改为函数表达式（如需作为值使用）
- [ ] 移除 `call`、`apply`、`bind` 使用，改用类方法
- [ ] 将 `is` 类型保护改为 `instanceof` + `as`
- [ ] 移除 `delete` 操作，使用 `null` 赋值
- [ ] 将字符串上的一元运算符改为显式转换
- [ ] 分离赋值语句表达式为独立语句
- [ ] 添加数组访问边界检查
- [ ] 将 Symbol 改为字符串字面量
- [ ] 移除生成器函数，使用 async/await
- [ ] 使用 `reactive()` 创建响应式状态对象
- [ ] 避免对象方法自引用，使用内部辅助函数

---

## 🔧 常见编译错误及解决方案

| 错误信息 | 原因 | 解决方案 |
|---------|------|----------|
| `Variable must be initialized` | 变量未初始化 | 声明时赋初值，即使是 `null` |
| `Cannot access before initialization` | 循环引用 | 使用内部辅助函数 |
| `Property may be null` | 空值访问 | 使用 `?.` 或显式 `!= null` 检查 |
| `Type not assignable` | 类型不兼容 | 使用 `as` 进行类型转换 |
| `Index signature not supported` | 索引签名 | 改用具体类型定义或数组 |
| `Destructuring not supported` | 解构赋值 | 改为直接属性访问 |
| `Implicit conversion not allowed` | 隐式转换 | 使用显式布尔判断或转换函数 |

此指南涵盖了 UTS 与 TypeScript 的主要差异，遵循这些规则可以确保代码成功编译并在跨端环境中正确运行。
