# Store 管理组件编写指南

基于 uni-app x 和 UTS 语言的全局状态管理组件开发模式，参考 Dialog 组件的最佳实践。

## 🏗️ 核心架构模式

### 1. 文件结构
```
components/
└── flyer-[component]/
    ├── flyer-[component].uvue    # 组件主体
    ├── store.uts                 # 状态管理
    └── README.md                 # 组件文档
```

### 2. Store 文件模板

创建 `store.uts` 文件，使用以下模板：

```uts
/**
 * Flyer [Component] Store - 全局状态管理
 * 基于 uni-app x UTS 语言实现
 */
import { reactive } from 'vue'

// 组件配置接口
type ComponentConfig = {
  title?: string
  content?: string
  // ... 其他配置项
  zIndex?: number
  id?: string
}

// 定义一个大写的 State 类型
export type State = {
  visible: boolean
  // ... 其他状态属性
  targetId: string | null
  zIndex: number
  resolvePromise: ((value: any) => void) | null
  rejectPromise: ((reason?: any) => void) | null
}

// 实例化为响应式 state
export const state = reactive({
  visible: false,
  // ... 默认值
  targetId: null,
  zIndex: 9999,
  resolvePromise: null,
  rejectPromise: null
} as State)

// 状态管理方法
export const mutations = {
  // 显示组件
  show(config?: ComponentConfig): Promise<any> {
    // 设置状态
    state.visible = true
    state.targetId = config?.id || null
    state.zIndex = config?.zIndex || 9999
    
    return new Promise<any>((resolve, reject) => {
      state.resolvePromise = resolve
      state.rejectPromise = reject
    })
  },

  // 确认操作
  confirm(value?: any) {
    state.visible = false
    const resolve = state.resolvePromise
    this.reset()
    
    if (resolve != null) {
      resolve(value || true)
    }
  },

  // 取消操作
  cancel(value?: any) {
    state.visible = false
    const resolve = state.resolvePromise
    this.reset()
    
    if (resolve != null) {
      resolve(value || false)
    }
  },

  // 关闭组件
  close() {
    state.visible = false
    const reject = state.rejectPromise
    this.reset()
    
    if (reject != null) {
      reject('closed')
    }
  },

  // 重置状态
  reset() {
    state.targetId = null
    state.resolvePromise = null
    state.rejectPromise = null
  }
}

// 便捷方法导出
export const showComponent = (config?: ComponentConfig): Promise<any> => {
  return mutations.show(config)
}
```

### 3. 组件主体模板

创建 `flyer-[component].uvue` 文件：

```vue
<template>
  <view v-if="shouldShow" class="flyer-[component]-overlay" :style="overlayStyle" @click="handleOverlayClick">
    <view class="flyer-[component]-modal" @click.stop="">
      <!-- 组件内容 -->
    </view>
  </view>
</template>

<script setup lang="uts">
import { computed } from 'vue'
import { state, mutations } from './store.uts'

// 定义组件选项
defineOptions({
  name: 'Flyer[Component]'
})

// 定义 props
const props = defineProps<{
  id?: string
}>()

// 计算是否应该显示组件
const shouldShow = computed(() => {
  // 如果没有设置 targetId，则兼容旧逻辑（所有组件都显示）
  if (state.targetId == null) {
    return state.visible
  }
  // 只有当 targetId 匹配当前组件 id 时才显示
  return state.visible && state.targetId == props.id
})

// 计算遮罩样式
const overlayStyle = computed(() => {
  return {
    zIndex: state.zIndex.toString()
  }
})

// 事件处理函数
function handleOverlayClick() {
  // 根据需要决定是否允许点击遮罩关闭
  handleCancel()
}

function handleConfirm() {
  mutations.confirm()
}

function handleCancel() {
  mutations.cancel()
}
</script>

<style>
.flyer-[component]-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 40px;
}

.flyer-[component]-modal {
  background-color: #ffffff;
  border-radius: 12px;
  overflow: hidden;
}
</style>
```

## 🔧 关键实现要点

### 1. 响应式状态 (必须)
```uts
import { reactive } from 'vue'

// ❌ 错误：非响应式状态
export const state: State = {
  visible: false
}

// ✅ 正确：使用 reactive 创建响应式状态
export const state = reactive({
  visible: false
} as State)
```

### 2. Promise 异步处理
```uts
// 所有显示方法都应该返回 Promise
show(config?: ComponentConfig): Promise<any> {
  state.visible = true
  
  return new Promise<any>((resolve, reject) => {
    state.resolvePromise = resolve
    state.rejectPromise = reject
  })
}
```

### 3. 多实例管理
```uts
// 通过 targetId 支持多实例
const shouldShow = computed(() => {
  if (state.targetId == null) {
    return state.visible
  }
  return state.visible && state.targetId == props.id
})
```

### 4. 状态重置
```uts
// 重置方法确保状态清理
reset() {
  state.targetId = null
  state.resolvePromise = null
  state.rejectPromise = null
}
```

## 📖 使用模式示例

### 1. 基础使用
```vue
<template>
  <flyer-component />
</template>

<script setup lang="uts">
import { mutations } from '@/components/flyer-component/store.uts'

async function showComponent() {
  try {
    const result = await mutations.show({
      title: '标题',
      content: '内容'
    })
    console.log('用户操作结果:', result)
  } catch (error) {
    console.log('组件被关闭')
  }
}
</script>
```

### 2. 多实例使用
```vue
<template>
  <flyer-component id="main" />
  <flyer-component id="secondary" />
</template>

<script setup lang="uts">
async function showMainComponent() {
  await mutations.show({
    id: 'main',
    title: '主组件'
  })
}

async function showSecondaryComponent() {
  await mutations.show({
    id: 'secondary', 
    title: '副组件'
  })
}
</script>
```

## 🎯 最佳实践

### 1. 类型安全
- 使用 TypeScript 类型声明
- 定义清晰的配置接口
- 导出类型供外部使用

### 2. 状态管理
- 所有状态变更通过 mutations
- 使用 reactive 创建响应式状态
- 及时清理状态，避免内存泄漏

### 3. Promise 处理
- 所有异步操作返回 Promise
- 正确处理 resolve 和 reject
- 提供 try-catch 错误处理示例

### 4. 组件设计
- 支持多实例管理
- 提供合理的默认值
- 遵循项目命名规范

## 🚀 开发流程

### 步骤1：创建 store.uts
1. 定义状态类型 State
2. 创建 reactive 响应式状态
3. 实现 mutations 方法
4. 添加便捷方法导出

### 步骤2：创建组件文件
1. 实现组件模板
2. 使用 computed 计算显示状态
3. 处理用户交互事件
4. 添加样式

### 步骤3：测试验证
1. 创建示例页面
2. 测试基础功能
3. 测试多实例场景
4. 验证响应式更新

### 步骤4：文档和导出
1. 编写 README 文档
2. 更新组件导出文件
3. 添加页面路由配置
4. 更新首页导航

## 🔍 常见问题

### Q: 为什么组件不响应状态变化？
A: 确保使用 `reactive()` 创建状态，而不是普通对象。

### Q: 如何支持多个实例？
A: 通过 `targetId` 和组件 `id` 属性实现实例区分。

### Q: Promise 如何正确处理？
A: 在 confirm/cancel 方法中调用 resolve，在 close 方法中调用 reject。

### Q: 状态如何清理？
A: 在每次操作完成后调用 reset() 方法清理状态。

## 📚 参考示例

- `components/flyer-dialog/` - 对话框组件完整实现
- `components/membership-upgrade/` - 原始参考实现
- `pages/examples/dialog/` - 使用示例

使用这个指南，可以快速创建具有全局状态管理的组件，确保代码的一致性和可维护性。
