---
mode: agent
---
# Flyer UI 开发规范

## UTS规范

### 基础类型和空值处理
- 表示为空有两种方式: | null 和 ?:
```
type obj = {
	id : number,
	name : string,
	age : number | null, // 可以设置为空### Vue 3 组合式 API 规范
- 必须使用 `<script setup lang="uts">` 语法。
- 使用 `defineProps<{}>()` 定义props类型。
- 使用 `defineEmits<{}>()` 定义事件类型。
- 响应式数据使用 `ref()` 或 `reactive()`。

### watch 监听器规范
- watch 回调函数必须明确声明返回 `void` 类型：
```
// ❌ 错误：回调函数未声明返回类型
watch(() => props.show, (newVal: boolean | null) => {
  visible.value = newVal == true
})

// ✅ 正确：明确声明返回 void 类型
watch(() => props.show, (newVal: boolean | null): void => {
  visible.value = newVal == true
}, { immediate: true })
```x ?: boolean // 可以设置为空
}
```
- 需要注意的是，使用 ?: 时，属性不可以传值，如：title?: string = null 是错误的，编译器会将?:识别为undefined，抛出异常Type 'null' is not assignable to type 'string | undefined'，只可使用title?: string 或 title: string = ''。
- UTS中，编译为安卓和ios环境时，不存在undefined，只有null，代码中需避免显示使用undefined。
- 所有变量必须初始化赋值，不能声明后不赋值：
```
// ❌ 错误：变量未初始化
let value: string | null

// ✅ 正确：变量必须初始化
let value: string | null = null
```

### 条件判断和类型转换
- 非空判断时，需明确使用null进行判断，不能使用隐式布尔转换：
```
// ✅ 正确使用
if(config.title != null) {
    state.title = config.title
}

// ❌ 错误使用：隐式转换
if(config.title) { }

// ❌ 错误使用：可选链赋值
state.title = config?.title
```

- 禁止使用 || 运算符进行类型自动转换，必须使用显式的三元运算符：
```
// ❌ 错误：|| 运算符类型转换
const value = props.zIndex || 996
const result = arr || []
const text = item.text || 'default'

// ✅ 正确：显式三元运算符
const value = props.zIndex != null ? props.zIndex : 996
const result = arr != null ? arr : []
const text = item.text != null ? item.text : 'default'
```

- 在模板的条件表达式中，|| 和 && 运算符是允许的：
```
<!-- ✅ 模板中可以使用 -->
<view v-if="visible || !isNvue">
<view v-if="(index !== 0 || tips != null) && theme === 'light'">
```

### watch 监听器规范
- watch 回调函数必须明确声明返回 `void` 类型：
```
// ❌ 错误：回调函数缺少明确的返回类型
watch(() => props.show, (newVal) => {
  visible.value = newVal == true
})

// ❌ 错误：可能的类型推断问题
watch(() => props.show, (newVal: boolean | null) => {
  visible.value = newVal == true // 可能导致类型不匹配
})

// ✅ 正确：明确声明void返回类型并处理null情况
watch(() => props.show, (newVal: boolean | null | undefined): void => {
  if (newVal == null) {
    visible.value = false
  } else {
    visible.value = newVal == true
  }
}, { immediate: true })

// ✅ 正确：简化的null安全处理
watch(() => props.show, (newVal: boolean | null | undefined): void => {
  visible.value = newVal == true
}, { immediate: true })
```
### 函数定义和调用规范
- UTS没有函数提升，函数必须在调用前定义：
```
// ❌ 错误：函数未定义就调用
function initData() {
    const result = checkIsValid() // checkIsValid 还未定义
}

function checkIsValid(): boolean {
    return true
}

// ✅ 正确：先定义后调用
function checkIsValid(): boolean {
    return true
}

function initData() {
    const result = checkIsValid() // checkIsValid 已定义
}
```

- 避免循环引用，使用内部辅助函数：
```
// ❌ 错误：对象方法自引用导致循环引用
export const mutations = {
  reset() {
    mutations.reset() // 循环引用错误
  }
}

// ✅ 正确：使用内部辅助函数
function resetState() {
  state.visible = false
  state.data = null
}

export const mutations = {
  reset() {
    resetState() // 调用内部函数
  }
}
```

### 动态属性访问规范
- 禁止使用 `any` 类型进行动态属性访问，必须使用 `UTSJSONObject`：
```
// ❌ 错误：直接访问动态属性
function handleClick(data: any): void {
  console.log(data.text) // 直接访问会编译错误
  uni.showToast({ title: data.text })
}

// ❌ 错误：使用 any 类型强制转换
const itemObj = item as any
return itemObj[textKey]

// ✅ 正确：使用 UTSJSONObject 并进行类型检查
function handleClick(data: any): void {
  const dataObj = data as UTSJSONObject
  const text = dataObj['text']
  const displayText = text != null && typeof text === 'string' ? text : '默认值'
  uni.showToast({ title: displayText })
}

// ✅ 正确：UTSJSONObject 动态属性访问
const itemObj = item as UTSJSONObject
const dynamicValue = itemObj[textKey]
if (dynamicValue != null && typeof dynamicValue === 'string') {
  return dynamicValue
}
return '默认值'
```

### 模板结构完整性规范
- 确保模板中所有元素都有完整的开始和结束标签：
```
// ❌ 错误：不完整的元素结构
<view>
  <view @tap="handleClick">
  </view>
</view>

// ✅ 正确：完整的元素结构
<view>
  <view @tap="handleClick">
    <text>按钮文字</text>
  </view>
</view>
```

### 文字元素使用规范
- **文字内容必须使用 `<text>` 标签，不能直接在 `<view>` 中放置文字**：
```
<!-- ❌ 错误：在 view 中直接放置文字 -->
<view>这是错误的文字显示方式</view>
<view class="title">标题文字</view>
<view @click="handleClick">按钮文字</view>

<!-- ✅ 正确：使用 text 标签包裹文字 -->
<view>
  <text>这是正确的文字显示方式</text>
</view>
<view class="title">
  <text>标题文字</text>
</view>
<view @click="handleClick">
  <text>按钮文字</text>
</view>

<!-- ✅ 正确：button 标签可以直接包含文字 -->
<button @click="handleClick">按钮文字</button>

<!-- ✅ 正确：input 和 textarea 等表单元素例外 -->
<input type="text" placeholder="请输入内容" />
<textarea placeholder="请输入多行文字"></textarea>
```

- **支持直接放置文字的标签**：
  - `<text>` - 文字显示的标准标签
  - `<button>` - 按钮标签可直接包含文字
  - `<input>` - 输入框通过 placeholder 等属性显示文字
  - `<textarea>` - 多行文本输入框
  - `<label>` - 标签元素（如果支持）

- **必须使用 text 包裹的场景**：
  - 所有在 `<view>` 中的文字内容
  - 列表项中的文字
  - 卡片组件中的标题和内容
  - 导航栏标题
  - 提示信息文字

### 对象类型定义
- 必须使用 `type` 定义对象类型，不能使用 `interface` 用于对象字面量：
```
// ❌ 错误：interface 用于对象字面量
interface Person {
  name: string
  age: number
}
const person: Person = { name: "John", age: 30 } // 编译错误

// ✅ 正确：使用 type 定义
type Person = {
  name: string
  age: number
}
const person: Person = { name: "John", age: 30 } // 正确
```

### Props 类型定义规范
- 对于复杂数组类型的 props（数组子项是对象），必须使用 `UTSJSONObject` 而不是自定义类型：
```
// ❌ 错误：使用自定义类型定义复杂数组 props
type MenuItem = {
  text: string
  value: string
  disabled?: boolean
}

const props = defineProps<{
  items: MenuItem[] // 编译错误：自定义类型不支持
}>()

// 或者在选项式 API 中
props: {
  items: {
    type: Array as PropType<MenuItem[]>, // 编译错误
    default: () => []
  }
}

// ✅ 正确：使用 UTSJSONObject 定义复杂数组 props
// 组合式 API
const props = defineProps<{
  items: UTSJSONObject[] // 使用 UTSJSONObject 数组
}>()

// 选项式 API
props: {
  items: {
    type: Array as PropType<UTSJSONObject[]>,
    default: () => []
  }
}

// ✅ 正确：访问 UTSJSONObject 数组元素的属性
function getItemText(item: UTSJSONObject): string {
  const text = item['text']
  return text != null && typeof text === 'string' ? text : ''
}

// ✅ 正确：处理复杂数组数据
function handleItemClick(item: UTSJSONObject): void {
  const itemObj = item as UTSJSONObject
  const value = itemObj['value']
  const disabled = itemObj['disabled']
  
  if (disabled == true) {
    return // 禁用状态不处理
  }
  
  if (value != null && typeof value === 'string') {
    // 处理点击逻辑
    emit('itemClick', value)
  }
}
```

- 简单数组类型可以直接使用基础类型：
```
// ✅ 正确：简单数组类型
const props = defineProps<{
  options: string[], // 字符串数组
  numbers: number[], // 数字数组
  flags: boolean[]   // 布尔数组
}>()

// 选项式 API
props: {
  options: {
    type: Array as PropType<string[]>,
    default: () => []
  },
  numbers: {
    type: Array as PropType<number[]>,
    default: () => []
  }
}
```

### 响应式状态管理
- 使用 `reactive()` 创建响应式对象，不能使用普通对象：
```
// ❌ 错误：普通对象不会触发响应式更新
export const state = {} as State

// ✅ 正确：使用 reactive 创建响应式对象
export const state = reactive({} as State)
```

### 安全导航和Promise处理
- Promise回调函数需要安全调用：
```
// ❌ 错误：直接调用可能为null的Promise函数
state.resolvePromise(value)

// ✅ 正确：使用安全导航
state.resolvePromise?.(value)

// ✅ 正确：显式null检查
if (state.resolvePromise != null) {
  state.resolvePromise(value)
}
```

### UTSJSONObject 操作和扩展运算符规范
- 禁止使用扩展运算符（...）进行对象合并：
```
// ❌ 错误：扩展运算符不支持
const mergedStyle = {
  ...baseStyle,
  ...customStyle
}

// ✅ 正确：手动复制属性进行对象合并
let mergedStyle = {} as UTSJSONObject
if (baseStyle != null) {
  const baseStyleObj = baseStyle as UTSJSONObject
  if (baseStyleObj['color'] != null) {
    mergedStyle['color'] = baseStyleObj['color']
  }
  if (baseStyleObj['backgroundColor'] != null) {
    mergedStyle['backgroundColor'] = baseStyleObj['backgroundColor']
  }
}
if (customStyle != null) {
  const customStyleObj = customStyle as UTSJSONObject
  if (customStyleObj['color'] != null) {
    mergedStyle['color'] = customStyleObj['color']
  }
  if (customStyleObj['backgroundColor'] != null) {
    mergedStyle['backgroundColor'] = customStyleObj['backgroundColor']
  }
}
```

- 禁止使用 `Object.keys()` 遍历 UTSJSONObject：
```
// ❌ 错误：Object.keys() 不支持 UTSJSONObject
const keys = Object.keys(this.customStyle)
for (let i = 0; i < keys.length; i++) {
  baseStyle[keys[i]] = this.customStyle[keys[i]]
}

// ❌ 错误：for...in 循环也不支持 UTSJSONObject
for (const key in this.customStyle) {
  baseStyle[key] = this.customStyle[key]
}

// ✅ 正确：手动复制已知属性
if (this.customStyle != null) {
  const customStyleObj = this.customStyle as UTSJSONObject
  if (customStyleObj['color'] != null) {
    baseStyle['color'] = customStyleObj['color']
  }
  if (customStyleObj['backgroundColor'] != null) {
    baseStyle['backgroundColor'] = customStyleObj['backgroundColor']
  }
  if (customStyleObj['fontSize'] != null) {
    baseStyle['fontSize'] = customStyleObj['fontSize']
  }
  if (customStyleObj['borderRadius'] != null) {
    baseStyle['borderRadius'] = customStyleObj['borderRadius']
  }
}
```

### 类型转换和赋值规范  
- 将外部对象赋值给 UTSJSONObject 时必须进行显式类型转换：
```
// ❌ 错误：直接赋值可能导致类型不匹配
baseStyle = this.customStyle

// ✅ 正确：使用 as 进行类型转换
baseStyle = this.customStyle as UTSJSONObject
```

### 模块状态访问规范（选项式 API）
- 在选项式API中，从外部模块导入的响应式状态无法直接在模板中访问，必须通过计算属性暴露：
```
// ❌ 错误：模板直接访问外部状态
<template>
  <text>{{ state.title }}</text>
</template>

<script lang="uts">
import { state } from './store.uts'
// 模板无法直接访问导入的 state
</script>

// ✅ 正确：通过计算属性暴露状态
<template>
  <text>{{ dialogTitle }}</text>
</template>

<script lang="uts">
import { state } from './store.uts'

export default {
  computed: {
    dialogTitle(): string | null {
      return state.title
    }
  }
}
</script>
```

### UTSJSONObject 类型转换和遍历规范
- 从UTSJSONObject取值时不能直接使用`String(value)`转换，需要安全的类型检查：
```
// ❌ 错误：直接类型转换会编译失败
queryParts.push(`${key}=${encodeURIComponent(String(value))}`)

// ✅ 正确：安全的类型转换
let stringValue: string = ''
if (typeof value === 'string') {
  stringValue = value
} else if (typeof value === 'number') {
  stringValue = value.toString()
} else if (typeof value === 'boolean') {
  stringValue = value ? 'true' : 'false'
} else {
  stringValue = JSON.stringify(value)
}
queryParts.push(`${key}=${encodeURIComponent(stringValue)}`)
```

- 不能使用`Object.keys()`遍历UTSJSONObject，需要使用for...in循环：
```
// ❌ 错误：Object.keys()不支持UTSJSONObject
const queryString = Object.keys(params)
  .map(key => `${key}=${encodeURIComponent(params[key] as string)}`)
  .join('&')

// ✅ 正确：使用for...in循环遍历
const queryParts: string[] = []
for (const key in params) {
  const value = params[key]
  if (value != null) {
    // 进行安全的类型转换
  }
}
const queryString = queryParts.join('&')
```

### 布尔判断和隐式转换规范
- 禁止使用隐式布尔转换，必须使用显式比较：
```
// ❌ 错误：隐式布尔转换
if (!params) return url
if (!url) throw new Error('Invalid URL')
if (!result.success) return { success: false, error }

// ✅ 正确：显式布尔比较
if (params == null) return url
if (url == null || url.length === 0) throw new Error('Invalid URL')
if (result.success == false) return { success: false, error }
```

### 动态属性访问规范扩展
- 不能直接使用点语法访问动态对象属性，必须使用索引语法：
```
// ❌ 错误：直接点语法访问动态属性
const pages = getCurrentPages()
const currentPage = pages[pages.length - 1]
return currentPage.route != null ? currentPage.route : ''

// ✅ 正确：使用索引语法访问
const pages = getCurrentPages()
const currentPage = pages[pages.length - 1]
const route = currentPage['route']
return route != null ? route as string : ''
```

### Unicode 字符串处理规范
- 避免直接使用 Unicode 字符串字面量，使用 String.fromCharCode() 构造：
```
// ❌ 错误：直接使用 Unicode 字符串可能编译失败
const closeIcon = '\ue005'

// ✅ 正确：使用 String.fromCharCode() 构造
const closeIcon = String.fromCharCode(0xe005)
```

### 导入和命名规范
- 文件使用import导入时，导出函数和变量，不可与当前文件中的同名变量命名相同，避免命名冲突。
- 无需显示导入vue：import { reactive } from 'vue'，uts中可直接使用reactive这类vue的api。

## uvue文件中的CSS规范
- 仅支持类名选择器，伪类选择器（如:active、:hover等）不支持。
- `line-height` 只支持 `<text>|<button>|<textarea>`。
- `white-space` 只支持 `<text>|<button>`。
- `font-size|text-align|color` 只支持 `<text>|<button>|<input>|<textarea>`。
- `text-align` 只支持 `<text>|<button>|<input>|<textarea>`。
- `font-weight` 只包含 `normal`|`bold`|`400`|`700`
- `color`​ 不支持 `inherit`。
- **CSS 单位支持**：仅支持 `px`、`rpx`、`%` 三种单位，不支持 `vh`、`vw`、`em`、`rem`、`vmin`、`vmax` 等其他单位。
- **推荐使用 `rpx`**：rpx 是响应式像素单位，能自动适配不同屏幕尺寸，是 uni-app x 的首选单位。

### 内联样式规范
- **禁止使用对象语法的内联样式**，只支持字符串拼接方式：
```
<!-- ❌ 错误：对象语法内联样式不支持 -->
<text :style="{ color: item.color, fontSize: formatFontSize(item.size) }">文本</text>
<view :style="{ backgroundColor: bgColor, marginTop: spacing + 'rpx' }">内容</view>

<!-- ✅ 正确：字符串拼接方式 -->
<text :style="'color:' + item.color + ';font-size:' + item.size + 'rpx'">文本</text>
<view :style="'background-color:' + bgColor + ';margin-top:' + spacing + 'rpx'">内容</view>

<!-- ✅ 正确：多个样式属性拼接 -->
<view :style="'color:' + textColor + ';background-color:' + bgColor + ';font-size:' + fontSize + 'rpx;padding:' + padding + 'rpx'">
  多样式内容
</view>

<!-- ✅ 正确：条件样式 -->
<text :style="'color:' + (isActive ? activeColor : normalColor) + ';font-weight:' + (isActive ? 'bold' : 'normal')">
  条件样式文本
</text>
```

- **内联样式字符串格式要求**：
  - 属性名使用 kebab-case（如 `font-size`、`background-color`）
  - 属性值后必须带单位（如 `rpx`、`px`）
  - 多个样式用分号 `;` 分隔
  - 避免多余的空格，保持格式一致性

## Vue 3 选项式 API 规范
- 使用 `<script lang="uts">` 语法（而非 `<script setup>`）。
- 使用 `export default { }` 导出组件选项。
- 使用 `props` 选项定义属性类型和默认值。
- 使用 `emits` 选项定义事件。
- 响应式数据在 `data()` 函数中返回。
- 计算属性在 `computed` 选项中定义。
- 方法在 `methods` 选项中定义。
- 生命周期钩子直接在选项中定义（如 `mounted`、`created` 等）。

示例结构：
```
<script lang="uts">
export default {
  name: 'ComponentName',
  props: {
    title: {
      type: String,
      default: ''
    },
    visible: {
      type: Boolean,
      default: false
    }
  },
  emits: ['close', 'confirm'],
  data() {
    return {
      loading: false,
      items: [] as string[]
    }
  },
  computed: {
    displayTitle(): string {
      return this.title != null && this.title.length > 0 ? this.title : '默认标题'
    }
  },
  methods: {
    handleClick(): void {
      this.$emit('close')
    }
  },
  mounted(): void {
    // 组件挂载后的逻辑
  }
}
</script>
```

## 常见编译错误和解决方案
- `Variable must be initialized`：所有变量声明时必须初始化赋值。
- `Cannot access before initialization`：函数或变量在定义前被调用，需要重新排序。
- `Property may be null`：属性可能为null，需要使用安全导航或显式检查。
- `Type not assignable`：类型不兼容，使用 `as` 进行类型转换。
- `Unresolved reference`：访问未定义的属性或方法，检查拼写和作用域。
- `Cannot convert String(value)`: UTSJSONObject的值不能直接用String()转换，需要typeof检查。
- `Object.keys is not supported`: 不能对UTSJSONObject使用Object.keys()，改用for...in循环。
- `Implicit boolean conversion not allowed`: 不能使用!param等隐式转换，改用param == null。
- `Property 'route' does not exist`: 动态属性访问要用obj['prop']而不是obj.prop。
- `Custom type not supported in props`: props中的复杂数组不能使用自定义类型，必须使用UTSJSONObject[]。
- `PropType validation failed`: 复杂对象数组props必须声明为Array as PropType<UTSJSONObject[]>。
- `组件view不支持事件`：某些事件如 `touchmove.stop.prevent` 在view组件中不支持。
- `Unsupported Pseudo-class selector`：CSS中不支持伪类选择器如 `:active`、`:hover`。
- `Unsupported unit`：CSS中不支持 `vh`、`vw`、`em`、`rem` 等单位，仅支持 `px`、`rpx`、`%`。
- `Unsupported style object syntax`：内联样式不支持对象语法如 `:style="{ color: red }"`，必须使用字符串拼接。
- `Style property format error`：内联样式属性格式错误，必须使用 kebab-case 并带单位。
- `Text content not allowed in view`：view 标签中不能直接放置文字，必须使用 text 标签包裹。
- `Invalid text rendering`：文字渲染错误，检查是否在非文字标签中直接放置了文字内容。
- 类型转换错误：禁止使用 || 运算符进行隐式类型转换。
- 动态属性访问错误：直接访问 `object.property` 需要改为 UTSJSONObject 模式。
- watch 回调类型错误：回调函数必须明确声明返回 `void` 类型并正确处理 null 情况。
- `Spread syntax is not allowed`：扩展运算符不支持，需要手动复制对象属性。
- `Object.keys() is not supported`：Object.keys() 不能用于 UTSJSONObject，需要手动访问已知属性。
- `for...in is not supported`：for...in 循环不能用于 UTSJSONObject，需要逐个访问属性。
- `Template cannot access imported state`：选项式 API 中模板无法直接访问外部导入的状态，需要通过计算属性暴露。
- `Unicode string literal error`：直接使用 Unicode 字符串可能失败，使用 String.fromCharCode() 构造。

## 开发检查清单
在提交代码前，请确保：
- [ ] 所有变量都已初始化赋值
- [ ] 没有使用 || 运算符进行类型转换（模板除外）
- [ ] 没有使用隐式布尔转换（!param改为param == null）
- [ ] 函数定义在调用之前
- [ ] 使用 `type` 定义对象类型而非 `interface`
- [ ] 动态属性访问使用 `UTSJSONObject` 而非直接访问
- [ ] 动态属性用索引语法obj['prop']而不是点语法obj.prop
- [ ] UTSJSONObject类型转换使用typeof检查而不是String()直接转换
- [ ] UTSJSONObject遍历使用for...in而不是Object.keys()
- [ ] 响应式对象使用 `reactive()` 创建
- [ ] Promise 回调使用安全导航或显式null检查
- [ ] 避免对象方法自引用，使用内部辅助函数
- [ ] CSS 只使用类选择器，避免伪类选择器
- [ ] CSS 单位仅使用 `px`、`rpx`、`%`，避免 `vh`、`vw`、`em`、`rem` 等不支持的单位
- [ ] **内联样式使用字符串拼接，避免对象语法 `:style="{ }"`**
- [ ] **内联样式属性使用 kebab-case 格式并带单位**
- [ ] **文字内容使用 `<text>` 标签包裹，不能直接在 `<view>` 中放置文字**
- [ ] **检查所有文字显示是否使用了正确的标签（text、button、input、textarea）**
- [ ] watch 回调函数明确声明返回 `void` 类型
- [ ] 模板元素结构完整，无缺失的开始或结束标签
- [ ] 不使用扩展运算符（...），改用手动属性复制
- [ ] 不使用 Object.keys() 或 for...in 遍历 UTSJSONObject
- [ ] 外部模块状态通过计算属性在模板中访问（选项式 API）
- [ ] Unicode 字符串使用 String.fromCharCode() 构造
- [ ] UTSJSONObject 赋值时进行显式类型转换（as UTSJSONObject）
