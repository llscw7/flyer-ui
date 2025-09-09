---
mode: agent
---
# UVUE 组件开发完整指南

基于 uni-app x 框架的 `.uvue` 组件开发规范，涵盖选项式API、组合式API、生命周期、响应式数据等核心特性。

## 🎯 UVUE 文件结构与基础语法

### 1. 基本文件结构
```uvue
<template>
  <!-- 模板内容 -->
  <view class="container">
    <text>{{ message }}</text>
  </view>
</template>

<script lang="uts">
  // 选项式 API
  export default {
    name: 'ComponentName',
    data() {
      return {
        message: 'Hello World'
      }
    }
  }
</script>

<!-- 或使用组合式 API -->
<script setup lang="uts">
  // 组合式 API
  const message = ref('Hello World')
</script>

<style>
.container {
  padding: 20px;
}
</style>
```

### 2. lang="uts" 必需项
```uvue
<!-- ✅ 必须指定 lang="uts" -->
<script lang="uts">
export default {
  // 组件配置
}
</script>

<script setup lang="uts">
// 组合式 API 代码
</script>

<!-- ❌ 错误：缺少 lang 属性 -->
<script>
export default {}
</script>
```

---

## 🔄 选项式 API 开发规范

### 1. 组件基础结构
```uvue
<script lang="uts">
export default {
  name: 'MyComponent',
  
  // Props 定义
  props: {
    title: {
      type: String,
      default: '默认标题'
    },
    count: {
      type: Number,
      default: 0
    },
    visible: {
      type: Boolean,
      default: false
    },
    options: {
      type: Object,
      default: () => {
        return {}
      }
    }
  },
  
  // 响应式数据
  data() {
    return {
      loading: false,
      items: [] as Array<string>,
      userInfo: {
        name: '',
        age: 0
      } as UserInfo
    }
  },
  
  // 计算属性
  computed: {
    displayText(): string {
      return this.title + ' - ' + this.count
    },
    
    isEmpty(): boolean {
      return this.items.length == 0
    }
  },
  
  // 监听器
  watch: {
    count: {
      handler(newVal: number, oldVal: number) {
        console.log('count changed:', oldVal, '->', newVal)
      },
      immediate: true
    }
  },
  
  // 生命周期
  onLoad() {
    console.log('页面加载')
  },
  
  onReady() {
    console.log('页面初次渲染完成')
  },
  
  // 方法定义
  methods: {
    handleClick(): void {
      this.loading = true
      // 处理点击事件
    },
    
    updateData(newData: UserInfo): void {
      this.userInfo = newData
    }
  }
}

// 类型定义
type UserInfo = {
  name: string,
  age: number
}
</script>
```

### 2. Props 定义最佳实践
```uvue
<script lang="uts">
export default {
  props: {
    // ✅ 基础类型定义
    text: {
      type: String,
      required: true
    },
    
    // ✅ 带默认值的可选属性
    size: {
      type: String,
      default: 'medium' // 可选值：small | medium | large
    },
    
    // ✅ 数字类型
    count: {
      type: Number,
      default: 0,
      validator: (value: number): boolean => {
        return value >= 0
      }
    },
    
    // ✅ 对象类型（使用函数返回默认值）
    config: {
      type: Object,
      default: () => {
        return {
          theme: 'light',
          animation: true
        }
      }
    },
    
    // ✅ 数组类型
    items: {
      type: Array,
      default: () => {
        return []
      }
    }
  }
}
</script>
```

---

## 🚀 组合式 API 开发规范

### 1. 基本组合式 API 结构
```uvue
<script setup lang="uts">
// 必要的导入
import { ref, reactive, computed, watch, onMounted, onUnmounted } from 'vue'

// 类型定义
type UserInfo = {
  name: string,
  age: number,
  email: string
}

type Config = {
  theme: string,
  language: string
}

// Props 定义
const props = defineProps<{
  title: string,
  count?: number,
  config?: Config
}>()

// 带默认值的 Props（推荐方式）
const props = withDefaults(defineProps<{
  title: string,
  count?: number,
  visible?: boolean
}>(), {
  count: 0,
  visible: false
})

// 事件定义
const emit = defineEmits<{
  click: [event: any],
  change: [value: string],
  update: [data: UserInfo]
}>()

// 响应式数据
const loading = ref(false)
const message = ref('')
const userInfo = reactive<UserInfo>({
  name: '',
  age: 0,
  email: ''
})

// 计算属性
const displayText = computed((): string => {
  return `${props.title} - ${props.count}`
})

// 监听器
watch(() => props.count, (newVal, oldVal) => {
  console.log('count changed:', oldVal, '->', newVal)
}, { immediate: true })

// 方法定义
function handleClick(): void {
  loading.value = true
  emit('click', { timestamp: Date.now() })
}

function updateUserInfo(info: Partial<UserInfo>): void {
  Object.assign(userInfo, info)
  emit('update', userInfo)
}

// 生命周期
onMounted(() => {
  console.log('组件已挂载')
  loadData()
})

onUnmounted(() => {
  console.log('组件即将卸载')
  cleanup()
})

// 暴露给父组件的方法
defineExpose({
  handleClick,
  updateUserInfo,
  reset: () => {
    loading.value = false
    message.value = ''
  }
})

// 私有方法
async function loadData(): Promise<void> {
  try {
    loading.value = true
    // 异步数据加载逻辑
  } finally {
    loading.value = false
  }
}

function cleanup(): void {
  // 清理逻辑
}
</script>
```

### 2. 组合函数（Composables）
```uvue
<script setup lang="uts">
// 自定义组合函数
function useCounter(initialValue: number = 0) {
  const count = ref(initialValue)
  
  const increment = (): void => {
    count.value++
  }
  
  const decrement = (): void => {
    count.value--
  }
  
  const reset = (): void => {
    count.value = initialValue
  }
  
  return {
    count: readonly(count),
    increment,
    decrement,
    reset
  }
}

// 使用组合函数
const { count, increment, decrement, reset } = useCounter(10)

function useToggle(initialValue: boolean = false) {
  const state = ref(initialValue)
  
  const toggle = (): void => {
    state.value = !state.value
  }
  
  const setTrue = (): void => {
    state.value = true
  }
  
  const setFalse = (): void => {
    state.value = false
  }
  
  return {
    state: readonly(state),
    toggle,
    setTrue,
    setFalse
  }
}
</script>
```

---

## 📱 模板语法与指令

### 1. 数据绑定
```uvue
<template>
  <!-- 文本插值 -->
  <text>{{ message }}</text>
  
  <!-- 属性绑定 -->
  <view :class="containerClass" :style="containerStyle">
    <text>内容</text>
  </view>
  
  <!-- 双向绑定 -->
  <input v-model="inputValue" placeholder="请输入内容" />
  
  <!-- 事件绑定 -->
  <button @click="handleClick" @tap="handleTap">
    点击按钮
  </button>
</template>

<script setup lang="uts">
const message = ref('Hello World')
const inputValue = ref('')
const isActive = ref(true)

const containerClass = computed((): string => {
  return isActive.value ? 'active' : 'inactive'
})

const containerStyle = computed(() => {
  return {
    backgroundColor: isActive.value ? '#007aff' : '#f8f8f8'
  }
})

function handleClick(): void {
  console.log('按钮被点击')
}

function handleTap(): void {
  console.log('按钮被轻触')
}
</script>
```

### 2. 条件渲染与列表渲染
```uvue
<template>
  <!-- 条件渲染 -->
  <view v-if="showContent">
    <text>显示内容</text>
  </view>
  <view v-else>
    <text>隐藏时显示</text>
  </view>
  
  <!-- v-show -->
  <view v-show="visible">
    <text>根据 visible 控制显示/隐藏</text>
  </view>
  
  <!-- 列表渲染 -->
  <view v-for="(item, index) in items" :key="item.id">
    <text>{{ index }} - {{ item.name }}</text>
  </view>
  
  <!-- 对象遍历 -->
  <view v-for="(value, key) in userInfo" :key="key">
    <text>{{ key }}: {{ value }}</text>
  </view>
</template>

<script setup lang="uts">
type Item = {
  id: number,
  name: string
}

const showContent = ref(true)
const visible = ref(false)

const items = ref<Item[]>([
  { id: 1, name: '项目1' },
  { id: 2, name: '项目2' }
])

const userInfo = reactive({
  name: '用户名',
  age: 25,
  email: 'user@example.com'
})
</script>
```

---

## 🔗 组件通信

### 1. 父子组件通信
```uvue
<!-- 父组件 -->
<template>
  <child-component 
    :title="parentTitle"
    :count="parentCount"
    @update="handleChildUpdate"
    @custom-event="handleCustomEvent"
  />
</template>

<script setup lang="uts">
import ChildComponent from './ChildComponent.uvue'

const parentTitle = ref('父组件标题')
const parentCount = ref(0)

function handleChildUpdate(data: any): void {
  console.log('子组件更新:', data)
}

function handleCustomEvent(value: string): void {
  console.log('自定义事件:', value)
}
</script>

<!-- 子组件 -->
<template>
  <view>
    <text>{{ title }} - {{ count }}</text>
    <button @click="sendUpdate">更新父组件</button>
  </view>
</template>

<script setup lang="uts">
const props = defineProps<{
  title: string,
  count: number
}>()

const emit = defineEmits<{
  update: [data: any],
  customEvent: [value: string]
}>()

function sendUpdate(): void {
  emit('update', {
    timestamp: Date.now(),
    from: 'child'
  })
  
  emit('customEvent', 'hello from child')
}
</script>
```

### 2. 组件引用与方法调用
```uvue
<template>
  <child-component ref="childRef" />
  <button @click="callChildMethod">调用子组件方法</button>
</template>

<script setup lang="uts">
import ChildComponent from './ChildComponent.uvue'

const childRef = ref<ComponentPublicInstance | null>(null)

function callChildMethod(): void {
  if (childRef.value != null) {
    // 调用子组件的方法
    childRef.value.$callMethod('updateData', { id: 1, name: 'new data' })
  }
}
</script>

<!-- 子组件需要使用 defineExpose 暴露方法 -->
<script setup lang="uts">
function updateData(data: any): void {
  console.log('收到父组件调用:', data)
}

defineExpose({
  updateData
})
</script>
```

---

## 🎨 样式开发规范

### 1. CSS 类名规范
```uvue
<template>
  <view class="component-container">
    <view class="component-header">
      <text class="component-title">标题</text>
    </view>
    <view class="component-content">
      <text class="component-text">内容</text>
    </view>
  </view>
</template>

<style>
/* ✅ 使用组件前缀避免样式冲突 */
.component-container {
  display: flex;
  flex-direction: column;
  padding: 20px;
}

.component-header {
  margin-bottom: 15px;
  padding: 10px;
  background-color: #f8f8f8;
}

.component-title {
  font-size: 18px;
  font-weight: bold;
  color: #333;
}

.component-content {
  flex: 1;
}

.component-text {
  font-size: 14px;
  color: #666;
  line-height: 1.5;
}

/* 状态样式 */
.component-container--active {
  border: 2px solid #007aff;
}

.component-container--disabled {
  opacity: 0.5;
  pointer-events: none;
}
</style>
```

### 2. 响应式样式
```uvue
<template>
  <view class="responsive-container" :class="containerClass">
    <text>响应式内容</text>
  </view>
</template>

<script setup lang="uts">
const props = defineProps<{
  size?: string, // small | medium | large
  type?: string  // primary | secondary | danger
}>()

const containerClass = computed((): string => {
  const classes: string[] = []
  
  if (props.size != null) {
    classes.push(`responsive-container--${props.size}`)
  }
  
  if (props.type != null) {
    classes.push(`responsive-container--${props.type}`)
  }
  
  return classes.join(' ')
})
</script>

<style>
.responsive-container {
  padding: 10px;
  border-radius: 4px;
}

/* 尺寸变体 */
.responsive-container--small {
  padding: 8px;
  font-size: 12px;
}

.responsive-container--medium {
  padding: 12px;
  font-size: 14px;
}

.responsive-container--large {
  padding: 16px;
  font-size: 16px;
}

/* 类型变体 */
.responsive-container--primary {
  background-color: #007aff;
  color: white;
}

.responsive-container--secondary {
  background-color: #8e8e93;
  color: white;
}

.responsive-container--danger {
  background-color: #ff3b30;
  color: white;
}
</style>
```

---

## 🔧 生命周期钩子

### 1. 页面生命周期（选项式API）
```uvue
<script lang="uts">
export default {
  // 页面加载时触发
  onLoad(options: OnLoadOptions) {
    console.log('页面加载', options)
    // 初始化页面数据
    this.initPageData()
  },
  
  // 页面初次渲染完成时触发
  onReady() {
    console.log('页面初次渲染完成')
    // 可以安全访问DOM元素
    this.setupUI()
  },
  
  // 页面显示时触发
  onShow() {
    console.log('页面显示')
    // 更新页面数据
    this.refreshData()
  },
  
  // 页面隐藏时触发
  onHide() {
    console.log('页面隐藏')
    // 暂停操作
    this.pauseOperations()
  },
  
  // 页面卸载时触发
  onUnload() {
    console.log('页面卸载')
    // 清理资源
    this.cleanup()
  },
  
  methods: {
    initPageData(): void {
      // 初始化逻辑
    },
    
    setupUI(): void {
      // UI设置逻辑
    },
    
    refreshData(): void {
      // 数据刷新逻辑
    },
    
    pauseOperations(): void {
      // 暂停操作逻辑
    },
    
    cleanup(): void {
      // 清理逻辑
    }
  }
}
</script>
```

### 2. 组合式API生命周期
```uvue
<script setup lang="uts">
import { 
  onLoad, onReady, onShow, onHide, onUnload,
  onMounted, onUpdated, onUnmounted
} from 'vue'

// 页面生命周期
onLoad((options) => {
  console.log('页面加载', options)
  initPageData()
})

onReady(() => {
  console.log('页面初次渲染完成')
  setupUI()
})

onShow(() => {
  console.log('页面显示')
  refreshData()
})

onHide(() => {
  console.log('页面隐藏')
  pauseOperations()
})

onUnload(() => {
  console.log('页面卸载')
  cleanup()
})

// 组件生命周期
onMounted(() => {
  console.log('组件已挂载')
})

onUpdated(() => {
  console.log('组件已更新')
})

onUnmounted(() => {
  console.log('组件即将卸载')
})

// 生命周期方法
function initPageData(): void {
  // 初始化逻辑
}

function setupUI(): void {
  // UI设置逻辑
}

function refreshData(): void {
  // 数据刷新逻辑
}

function pauseOperations(): void {
  // 暂停操作逻辑
}

function cleanup(): void {
  // 清理逻辑
}
</script>
```

---

## ⚠️ 常见问题与最佳实践

### 1. 类型安全
```uvue
<script setup lang="uts">
// ✅ 推荐：明确定义类型
type FormData = {
  username: string,
  age: number,
  active: boolean
}

const formData = reactive<FormData>({
  username: '',
  age: 0,
  active: false
})

// ✅ 推荐：函数参数和返回值类型
function processData(data: FormData): boolean {
  if (data.username.length == 0) {
    return false
  }
  return true
}

// ❌ 避免：使用 any 类型
// const formData: any = {}
</script>
```

### 2. 响应式数据处理
```uvue
<script setup lang="uts">
// ✅ 推荐：使用 ref 包装基础类型
const count = ref(0)
const message = ref('')

// ✅ 推荐：使用 reactive 包装对象
const userInfo = reactive({
  name: '',
  settings: {
    theme: 'light'
  }
})

// ✅ 推荐：数组操作
const items = ref<string[]>([])

function addItem(item: string): void {
  items.value.push(item)
}

function removeItem(index: number): void {
  items.value.splice(index, 1)
}

// ❌ 避免：直接修改 reactive 对象的根级属性
// userInfo = { name: 'new name' } // 错误
</script>
```

### 3. 事件处理最佳实践
```uvue
<template>
  <!-- ✅ 推荐：明确的事件处理 -->
  <button @click="handleClick">点击</button>
  
  <!-- ✅ 推荐：传递参数 -->
  <button @click="handleItemClick(item.id)">
    {{ item.name }}
  </button>
  
  <!-- ✅ 推荐：事件修饰符 -->
  <view @click.stop="handleClick">
    <button @click="handleButtonClick">按钮</button>
  </view>
</template>

<script setup lang="uts">
function handleClick(): void {
  console.log('通用点击处理')
}

function handleItemClick(id: number): void {
  console.log('项目点击:', id)
}

function handleButtonClick(): void {
  console.log('按钮点击')
}
</script>
```

---

## 📋 开发检查清单

### 基础结构检查
- [ ] 使用 `<script lang="uts">` 或 `<script setup lang="uts">`
- [ ] 组件命名遵循 PascalCase
- [ ] Props 定义包含类型和默认值
- [ ] 事件名使用 kebab-case

### 类型安全检查
- [ ] 所有变量都有明确类型
- [ ] 函数参数和返回值有类型注解
- [ ] 避免使用 `any` 类型
- [ ] Props 和 Emit 有完整类型定义

### 响应式数据检查
- [ ] 基础类型使用 `ref()`
- [ ] 对象类型使用 `reactive()`
- [ ] 计算属性使用 `computed()`
- [ ] 监听器使用 `watch()` 或 `watchEffect()`

### 生命周期检查
- [ ] 正确使用页面和组件生命周期
- [ ] 在 `onUnmounted` 中清理资源
- [ ] 避免在生命周期中进行大量同步操作

### 样式规范检查
- [ ] CSS 类名使用组件前缀
- [ ] 避免使用全局样式
- [ ] 响应式样式使用计算属性
- [ ] 颜色和尺寸使用变量定义

### 性能优化检查
- [ ] 合理使用 `v-if` 和 `v-show`
- [ ] 列表渲染使用唯一 `key`
- [ ] 避免在模板中使用复杂表达式
- [ ] 大数据列表考虑虚拟滚动

此指南涵盖了 UVUE 组件开发的核心要点，遵循这些规范可以确保组件的质量、可维护性和跨端兼容性。

````
