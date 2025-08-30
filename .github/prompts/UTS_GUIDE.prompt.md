---
mode: agent
---
# Flyer UI 开发规范

## Vue 3 组合式 API 规范

### 必须使用组合式 API

**本项目已全面采用 Vue 3 组合式 API，所有 uvue 文件必须使用 `<script setup lang="uts">` 语法。**

```vue
<script setup lang="uts">
// 导入响应式 API
import { ref, reactive, computed, watch, onMounted } from 'vue'

// 定义组件选项
defineOptions({
  name: 'FlyerComponent'
})

// 定义 props
const props = defineProps<{
  type?: string,
  disabled?: boolean
}>()

// 定义事件
const emit = defineEmits<{
  click: [event: any]
}>()

// 响应式数据
const isLoading = ref(false)
const formData = reactive({
  name: '',
  age: 0
})

// 计算属性
const computedClass = computed(() => {
  return `component--${props.type || 'default'}`
})

// 方法定义
function handleClick(event: any) {
  if (props.disabled) {
    return
  }
  emit('click', event)
}
</script>
```

## UTS 语言规范

### 基本原则

1. **严格类型匹配**：UTS是强类型语言，不允许隐式类型转换
2. **条件语句类型要求**：条件语句必须使用布尔类型
3. **空值安全**：严格区分可为null和不可为null的类型
4. **避免any类型**：尽量使用具体类型，减少any的使用

### 类型定义规范

```uts
// ❌ 错误：不要使用TypeScript的联合类型导出
export type ButtonType = 'primary' | 'success' | 'warning'

// ✅ 正确：在props中直接使用String类型，通过注释说明可选值
props: {
  // 按钮类型：primary | success | warning | danger | info | default
  type: {
    type: String,
    default: 'default'
  }
}
```

### 条件判断规范

```uts
// ❌ 错误：不能使用隐式转换
if (value) { }
if (!value) { }

// ✅ 正确：明确的布尔判断
if (value != null) { }
if (value == null) { }
if (value == true) { }
if (value == false) { }
if (value.length > 0) { }
```

### 空值处理规范

```uts
// ✅ 可为null的类型定义
let value: string | null = null

// ✅ 安全调用
value?.toUpperCase()

// ⚠️  谨慎使用断言
value!.toUpperCase()

// ✅ 类型判断后使用
if (value != null) {
  value.toUpperCase()
}
```

### 对象类型规范

```uts
// ❌ 错误：不支持object类型
let data: object = {}

// ✅ 正确：使用UTSJSONObject
let data: UTSJSONObject = {}

// ❌ 错误：使用interface定义对象类型
interface ButtonProps {
  type: string
}

// ✅ 正确：使用type定义对象类型
type ButtonProps = {
  type: string,
  disabled: boolean
}
```

### 变量声明规范

```uts
// ❌ 错误：不使用var
var name = 'test'

// ✅ 正确：使用let和const
let name = 'test'
const config = { type: 'primary' }

// ✅ 显式类型声明
let count: number = 0
const isVisible: boolean = true
```

### 函数定义规范

```uts
// ✅ 方法定义
methods: {
  handleClick(event: any) {
    // 处理逻辑
  },
  
  // 带返回值的方法
  formatText(text: string): string {
    return text.toUpperCase()
  }
}
```

### 数组操作规范

```uts
// ✅ 类型化数组
let items: string[] = ['a', 'b', 'c']
let numbers: number[] = [1, 2, 3]

// ✅ 数组判断
if (items.length > 0) {
  // 处理逻辑
}
```

## 组件开发规范

### 组件命名规范

```uts
// ✅ 组件名称使用 flyer- 前缀
export default {
  name: 'FlyerButton'  // 组件名称
}
```

### Props 定义规范

```uts
props: {
  // 基础类型
  text: {
    type: String,
    default: ''
  },
  
  // 布尔类型
  disabled: {
    type: Boolean,
    default: false
  },
  
  // 数字类型
  count: {
    type: Number,
    default: 0
  },
  
  // 对象类型
  customStyle: {
    type: Object,
    default: () => {
      return {}
    }
  }
}
```

### 事件处理规范

```uts
methods: {
  handleClick(event: any) {
    // 禁用状态检查
    if (this.disabled) {
      return
    }
    
    // 触发自定义事件
    this.$emit('click', event)
  },
  
  handleChange(value: any) {
    this.$emit('change', value)
  }
}
```

### 样式规范

```css
/* ✅ 正确：使用flex布局 */
.flyer-button {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
}

/* ✅ 正确：只使用类选择器 */
.flyer-button--primary {
  background-color: #007aff;
}

/* ❌ 错误：不要使用复杂选择器 */
.flyer-button .inner > .text {
  color: red;
}

/* ✅ 正确：文字样式只能用于text组件 */
.flyer-button__text {
  color: #333333;
  font-size: 14px;
}
```

### 条件编译规范

```uts
// ✅ 平台条件编译
// #ifdef APP-ANDROID
// Android特有逻辑
// #endif

// #ifdef APP-IOS
// iOS特有逻辑
// #endif

// #ifdef APP
// APP通用逻辑
// #endif
```

## 文件结构规范

### 组件目录结构

```
components/
└── flyer-button/
    └── flyer-button.uvue    # 组件实现文件
```

### 页面目录结构

```
pages/
├── index/
│   └── index.uvue          # 首页
└── examples/
    └── button/
        └── button.uvue     # 组件示例页面
```

## 错误处理规范

### 常见错误及解决方案

1. **类型转换错误**
```uts
// ❌ 错误
let str = 123 + ''

// ✅ 正确
let str = String(123)
```

2. **条件判断错误**
```uts
// ❌ 错误
if (array.length) { }

// ✅ 正确
if (array.length > 0) { }
```

3. **空值访问错误**
```uts
// ❌ 错误
let result = data.name

// ✅ 正确
let result = data?.name ?? ''
```

## 性能优化规范

1. **避免在模板中使用复杂计算**
2. **合理使用v-if和v-show**
3. **避免深层嵌套的响应式数据**
4. **及时清理事件监听器**

## 测试规范

1. **每个组件都应该有对应的示例页面**
2. **测试各种props组合**
3. **测试边界情况**
4. **测试平台兼容性**

---

## 检查清单

在提交代码前，请确保：

- [ ] 所有变量都有明确类型
- [ ] 条件语句使用布尔类型
- [ ] 没有使用TypeScript特有语法
- [ ] 样式遵循ucss规范
- [ ] 组件支持easycom自动注册
- [ ] 提供了使用示例
- [ ] 测试了基本功能
