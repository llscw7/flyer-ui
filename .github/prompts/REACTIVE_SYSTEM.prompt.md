---
mode: agent
---
# UNI-APP X 响应式系统开发指南

基于 uni-app x 框架的响应式 API 使用规范，涵盖 ref、reactive、computed、watch 等核心响应式特性。

## 📊 响应式数据创建

### 1. ref() - 基础类型响应式
```uts
// 基础类型使用 ref()
const count = ref(0)
const message = ref('Hello World')
const isVisible = ref(true)
const price = ref(99.99)

// 带类型注解的 ref
const userCount = ref<number>(0)
const userName = ref<string>('')
const isOnline = ref<boolean>(false)

// 可为 null 的 ref
const selectedItem = ref<UserInfo | null>(null)
const currentUser = ref<User | null>(null)

// 数组 ref
const tags = ref<string[]>([])
const userList = ref<UserInfo[]>([])

// 访问和修改 ref 值
function updateData(): void {
  count.value = 10
  message.value = 'Updated'
  tags.value.push('new-tag')
  
  // 替换整个数组
  userList.value = [
    { id: 1, name: 'User1' },
    { id: 2, name: 'User2' }
  ]
}

// 在模板中自动解包，无需 .value
// <text>{{ count }}</text>  // 自动显示 count.value
```

### 2. reactive() - 对象响应式
```uts
// 对象类型使用 reactive()
type UserInfo = {
  id: number,
  name: string,
  email: string,
  profile: {
    avatar: string,
    bio: string
  }
}

const userInfo = reactive<UserInfo>({
  id: 0,
  name: '',
  email: '',
  profile: {
    avatar: '',
    bio: ''
  }
})

// 表单状态管理
type FormState = {
  username: string,
  password: string,
  rememberMe: boolean,
  errors: {
    username?: string,
    password?: string
  }
}

const formState = reactive<FormState>({
  username: '',
  password: '',
  rememberMe: false,
  errors: {}
})

// 列表状态管理
type ListState = {
  items: UserInfo[],
  loading: boolean,
  total: number,
  currentPage: number
}

const listState = reactive<ListState>({
  items: [],
  loading: false,
  total: 0,
  currentPage: 1
})

// 修改响应式对象
function updateUserInfo(): void {
  // 直接修改属性
  userInfo.name = 'New Name'
  userInfo.profile.bio = 'Updated bio'
  
  // 批量更新
  Object.assign(userInfo, {
    name: 'Batch Update',
    email: 'new@example.com'
  })
}
```

### 3. ref() vs reactive() 选择规则
```uts
// ✅ 使用 ref() 的场景
const count = ref(0)                    // 基础类型
const isLoading = ref(false)            // 布尔值
const selectedId = ref<number | null>(null) // 可选基础类型
const itemList = ref<Item[]>([])        // 数组（需要替换整个数组）

// ✅ 使用 reactive() 的场景
const userInfo = reactive({             // 对象状态
  name: '',
  age: 0
})

const formData = reactive({             // 表单数据
  username: '',
  email: '',
  settings: {
    theme: 'light'
  }
})

// ❌ 避免的用法
// const obj = ref({ name: '', age: 0 })  // 对象应该用 reactive
// const name = reactive('string')        // 基础类型应该用 ref
```

---

## 🧮 计算属性与监听器

### 1. computed() - 计算属性
```uts
// 基础计算属性
const count = ref(0)
const doubleCount = computed((): number => {
  return count.value * 2
})

// 复杂计算属性
const userInfo = reactive({
  firstName: '',
  lastName: '',
  age: 0
})

const fullName = computed((): string => {
  return `${userInfo.firstName} ${userInfo.lastName}`
})

const ageCategory = computed((): string => {
  if (userInfo.age < 18) return 'minor'
  if (userInfo.age < 60) return 'adult'
  return 'senior'
})

// 列表筛选计算属性
const items = ref<Item[]>([])
const searchText = ref('')
const selectedCategory = ref('')

const filteredItems = computed((): Item[] => {
  let result = items.value
  
  if (searchText.value.length > 0) {
    result = result.filter(item => 
      item.name.includes(searchText.value)
    )
  }
  
  if (selectedCategory.value.length > 0) {
    result = result.filter(item => 
      item.category === selectedCategory.value
    )
  }
  
  return result
})

// 可写计算属性
const firstName = ref('')
const lastName = ref('')

const fullName = computed({
  get(): string {
    return `${firstName.value} ${lastName.value}`
  },
  set(value: string): void {
    const names = value.split(' ')
    firstName.value = names[0] || ''
    lastName.value = names[1] || ''
  }
})
```

### 2. watch() - 监听器
```uts
// 监听 ref
const count = ref(0)

watch(count, (newValue: number, oldValue: number) => {
  console.log(`count changed: ${oldValue} -> ${newValue}`)
})

// 监听多个源
const x = ref(0)
const y = ref(0)

watch([x, y], ([newX, newY]: [number, number], [oldX, oldY]: [number, number]) => {
  console.log(`coordinates changed: (${oldX}, ${oldY}) -> (${newX}, ${newY})`)
})

// 监听响应式对象的属性
const userInfo = reactive({
  name: '',
  age: 0
})

watch(() => userInfo.name, (newName: string, oldName: string) => {
  console.log(`name changed: ${oldName} -> ${newName}`)
})

// 深度监听
watch(userInfo, (newValue, oldValue) => {
  console.log('userInfo changed deeply')
}, { deep: true })

// 立即执行监听器
watch(count, (value: number) => {
  console.log('count:', value)
}, { immediate: true })

// 监听器配置选项
watch(
  () => userInfo.name,
  (newName: string) => {
    // 处理名称变化
    validateName(newName)
    updateProfile(newName)
  },
  {
    immediate: true,  // 立即执行
    deep: false,      // 不深度监听
    flush: 'post'     // DOM 更新后执行
  }
)
```

### 3. watchEffect() - 自动依赖收集
```uts
// 基础用法 - 自动追踪依赖
const count = ref(0)
const multiplier = ref(2)

watchEffect(() => {
  console.log(`Result: ${count.value * multiplier.value}`)
})

// 异步副作用
const userId = ref<number | null>(null)
const userData = ref<UserInfo | null>(null)

watchEffect(async () => {
  if (userId.value != null) {
    try {
      userData.value = await fetchUser(userId.value)
    } catch (error) {
      console.error('Failed to fetch user:', error)
      userData.value = null
    }
  }
})

// 清理副作用
watchEffect((onCleanup) => {
  const timer = setInterval(() => {
    console.log('Timer tick')
  }, 1000)
  
  onCleanup(() => {
    clearInterval(timer)
  })
})

// 条件性副作用
const isEnabled = ref(true)
const data = ref('')

watchEffect(() => {
  if (isEnabled.value) {
    console.log('Processing data:', data.value)
    // 只有当 isEnabled 为 true 时才处理数据
  }
})
```

---

## 🔄 响应式工具函数

### 1. readonly() - 只读响应式
```uts
// 创建只读响应式数据
const original = reactive({
  count: 0,
  name: 'original'
})

const readonlyData = readonly(original)

// ✅ 可以读取
console.log(readonlyData.count)

// ❌ 不能修改（在 TypeScript 中会报错）
// readonlyData.count = 1  // 错误

// 在组合函数中暴露只读状态
function useCounter() {
  const state = reactive({
    count: 0,
    step: 1
  })
  
  const increment = (): void => {
    state.count += state.step
  }
  
  return {
    state: readonly(state),  // 暴露只读状态
    increment
  }
}
```

### 2. toRefs() - 解构响应式对象
```uts
// 将响应式对象转换为 ref
const userInfo = reactive({
  name: 'John',
  age: 25,
  email: 'john@example.com'
})

// 解构后保持响应式
const { name, age, email } = toRefs(userInfo)

// 现在可以单独使用每个 ref
watch(name, (newName: string) => {
  console.log('Name changed:', newName)
})

// 在组合函数中返回解构的状态
function useUser() {
  const userState = reactive({
    info: { name: '', age: 0 },
    loading: false,
    error: null
  })
  
  return {
    ...toRefs(userState),  // 解构返回
    updateUser: (info: UserInfo) => {
      userState.info = info
    }
  }
}

// 使用解构的状态
const { info, loading, error, updateUser } = useUser()
```

### 3. unref() - 获取响应式值
```uts
// 获取 ref 或普通值
function processValue<T>(value: T | Ref<T>): T {
  return unref(value)  // 如果是 ref 则返回 .value，否则返回原值
}

const refValue = ref(42)
const normalValue = 42

console.log(processValue(refValue))    // 42
console.log(processValue(normalValue)) // 42

// 在工具函数中处理可能是 ref 的值
function formatPrice(price: number | Ref<number>, currency: string = '¥'): string {
  return `${currency}${unref(price).toFixed(2)}`
}
```

---

## 🏗️ 组合式函数开发

### 1. 状态管理组合函数
```uts
// 计数器组合函数
export function useCounter(initialValue: number = 0) {
  const count = ref(initialValue)
  
  const increment = (step: number = 1): void => {
    count.value += step
  }
  
  const decrement = (step: number = 1): void => {
    count.value -= step
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

// 切换状态组合函数
export function useToggle(initialValue: boolean = false) {
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

// 列表管理组合函数
export function useList<T>(initialItems: T[] = []) {
  const items = ref<T[]>(initialItems)
  
  const add = (item: T): void => {
    items.value.push(item)
  }
  
  const remove = (index: number): void => {
    items.value.splice(index, 1)
  }
  
  const removeById = (id: any): void => {
    const index = items.value.findIndex((item: any) => item.id === id)
    if (index > -1) {
      items.value.splice(index, 1)
    }
  }
  
  const clear = (): void => {
    items.value = []
  }
  
  const length = computed((): number => items.value.length)
  const isEmpty = computed((): boolean => items.value.length === 0)
  
  return {
    items: readonly(items),
    add,
    remove,
    removeById,
    clear,
    length,
    isEmpty
  }
}
```

### 2. 异步状态组合函数
```uts
// 异步请求状态管理
export function useAsyncState<T>() {
  const data = ref<T | null>(null)
  const loading = ref(false)
  const error = ref<Error | null>(null)
  
  const execute = async (asyncFn: () => Promise<T>): Promise<void> => {
    try {
      loading.value = true
      error.value = null
      data.value = await asyncFn()
    } catch (err) {
      error.value = err as Error
    } finally {
      loading.value = false
    }
  }
  
  const reset = (): void => {
    data.value = null
    error.value = null
    loading.value = false
  }
  
  const isReady = computed((): boolean => {
    return !loading.value && error.value == null && data.value != null
  })
  
  return {
    data: readonly(data),
    loading: readonly(loading),
    error: readonly(error),
    execute,
    reset,
    isReady
  }
}

// 分页数据组合函数
export function usePagination<T>() {
  const items = ref<T[]>([])
  const total = ref(0)
  const currentPage = ref(1)
  const pageSize = ref(10)
  const loading = ref(false)
  
  const totalPages = computed((): number => {
    return Math.ceil(total.value / pageSize.value)
  })
  
  const hasNextPage = computed((): boolean => {
    return currentPage.value < totalPages.value
  })
  
  const hasPrevPage = computed((): boolean => {
    return currentPage.value > 1
  })
  
  const loadPage = async (
    page: number,
    fetchFn: (page: number, size: number) => Promise<{ items: T[], total: number }>
  ): Promise<void> => {
    try {
      loading.value = true
      const result = await fetchFn(page, pageSize.value)
      items.value = result.items
      total.value = result.total
      currentPage.value = page
    } catch (error) {
      console.error('Failed to load page:', error)
    } finally {
      loading.value = false
    }
  }
  
  const nextPage = async (fetchFn: (page: number, size: number) => Promise<{ items: T[], total: number }>): Promise<void> => {
    if (hasNextPage.value) {
      await loadPage(currentPage.value + 1, fetchFn)
    }
  }
  
  const prevPage = async (fetchFn: (page: number, size: number) => Promise<{ items: T[], total: number }>): Promise<void> => {
    if (hasPrevPage.value) {
      await loadPage(currentPage.value - 1, fetchFn)
    }
  }
  
  return {
    items: readonly(items),
    total: readonly(total),
    currentPage: readonly(currentPage),
    pageSize: readonly(pageSize),
    loading: readonly(loading),
    totalPages,
    hasNextPage,
    hasPrevPage,
    loadPage,
    nextPage,
    prevPage
  }
}
```

---

## ⚡ 性能优化技巧

### 1. 响应式优化
```uts
// ✅ 使用 shallowRef 优化大对象
const largeObject = shallowRef({
  // 大量数据...
  items: new Array(10000).fill(0).map(i => ({ id: i, data: `item-${i}` }))
})

// ✅ 使用 markRaw 标记非响应式对象
const nonReactiveData = markRaw({
  staticConfig: { /* 静态配置 */ },
  constants: { /* 常量数据 */ }
})

// ✅ 合理使用计算属性缓存
const expensiveComputed = computed((): string => {
  // 只有依赖变化时才重新计算
  return performExpensiveCalculation(data.value)
})

// ❌ 避免在模板中直接调用函数
// <text>{{ expensiveFunction(data) }}</text>  // 每次都会调用

// ✅ 使用计算属性替代
const processedData = computed((): string => {
  return expensiveFunction(data.value)
})
// <text>{{ processedData }}</text>
```

### 2. 监听器优化
```uts
// ✅ 精确监听需要的属性
watch(() => user.value.name, (newName) => {
  // 只监听 name 属性
})

// ❌ 避免不必要的深度监听
// watch(user, callback, { deep: true })  // 监听整个对象的所有变化

// ✅ 使用 watchEffect 自动优化依赖
watchEffect(() => {
  // 自动收集依赖，只在相关数据变化时执行
  if (isVisible.value) {
    updateUI(data.value)
  }
})

// ✅ 及时停止监听器
const stop = watch(data, callback)

onUnmounted(() => {
  stop()  // 组件卸载时停止监听
})
```

---

## 📋 开发检查清单

### 响应式数据检查
- [ ] 基础类型使用 `ref()`
- [ ] 对象类型使用 `reactive()`
- [ ] 提供明确的类型注解
- [ ] 合理使用 `readonly()` 保护数据

### 计算属性检查
- [ ] 计算属性函数为纯函数
- [ ] 避免在计算属性中修改响应式数据
- [ ] 复杂计算使用 `computed()` 缓存
- [ ] 可写计算属性正确实现 get/set

### 监听器检查
- [ ] 精确监听需要的数据
- [ ] 异步操作正确处理错误
- [ ] 副作用及时清理
- [ ] 组件卸载时停止监听器

### 组合函数检查
- [ ] 返回只读状态和操作方法
- [ ] 提供清晰的类型定义
- [ ] 合理的抽象层次
- [ ] 可复用性良好

### 性能优化检查
- [ ] 避免不必要的深度监听
- [ ] 大对象使用 `shallowRef`
- [ ] 静态数据使用 `markRaw`
- [ ] 及时清理副作用

此指南涵盖了 uni-app x 响应式系统的核心用法和最佳实践，有助于构建高性能、可维护的响应式应用。

````
