# Store 组件快速参考

## 🚀 快速创建步骤

### 1. 创建 store.uts
```uts
import { reactive } from 'vue'

export type State = {
  visible: boolean
  targetId: string | null
  resolvePromise: ((value: any) => void) | null
}

export const state = reactive({
  visible: false,
  targetId: null,
  resolvePromise: null
} as State)

export const mutations = {
  show(config?: { id?: string }): Promise<any> {
    state.visible = true
    state.targetId = config?.id || null
    return new Promise((resolve) => {
      state.resolvePromise = resolve
    })
  },
  
  confirm(value?: any) {
    state.visible = false
    if (state.resolvePromise) {
      state.resolvePromise(value || true)
      state.resolvePromise = null
    }
  },
  
  cancel() {
    state.visible = false
    if (state.resolvePromise) {
      state.resolvePromise(false)
      state.resolvePromise = null
    }
  }
}
```

### 2. 创建组件 uvue
```vue
<template>
  <view v-if="shouldShow" class="overlay" @click="handleCancel">
    <view class="modal" @click.stop="">
      <!-- 内容 -->
    </view>
  </view>
</template>

<script setup lang="uts">
import { computed } from 'vue'
import { state, mutations } from './store.uts'

const props = defineProps<{ id?: string }>()

const shouldShow = computed(() => {
  return state.targetId == null ? 
    state.visible : 
    state.visible && state.targetId == props.id
})

function handleCancel() {
  mutations.cancel()
}
</script>
```

### 3. 使用方式
```vue
<template>
  <flyer-component />
</template>

<script setup lang="uts">
import { mutations } from '@/components/flyer-component/store.uts'

async function show() {
  const result = await mutations.show()
  console.log(result)
}
</script>
```

## ⚡ 关键要点

- ✅ 使用 `reactive()` 创建响应式状态
- ✅ Promise 返回用户操作结果
- ✅ 支持多实例 (targetId)
- ✅ 及时清理 resolvePromise
- ❌ 不要忘记 `@click.stop=""` 阻止事件冒泡
