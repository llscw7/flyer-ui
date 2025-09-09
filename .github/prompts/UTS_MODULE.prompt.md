---
mode: agent
---
# UTS 状态管理与模块化开发指南

基于 uni-app x 框架的 UTS 文件开发规范，涵盖状态管理、模块导入导出、工具函数等核心特性。

## 🏗️ UTS 文件结构与模块化

### 1. 基本模块结构
```uts
// store/user.uts - 用户状态管理模块

// 类型定义放在顶部
export type UserInfo = {
  id: number,
  name: string,
  email: string,
  avatar: string,
  role: string
}

export type UserState = {
  currentUser: UserInfo | null,
  isLogin: boolean,
  token: string | null,
  permissions: string[]
}

// 导入必要的响应式API
import { reactive, computed, readonly } from 'vue'

// 状态定义
export const state = reactive<UserState>({
  currentUser: null,
  isLogin: false,
  token: null,
  permissions: []
})

// 计算属性
export const isAdmin = computed((): boolean => {
  return state.currentUser?.role === 'admin'
})

export const hasPermission = computed(() => {
  return (permission: string): boolean => {
    return state.permissions.includes(permission)
  }
})

// 状态更新方法
export const setUser = (user: UserInfo): void => {
  state.currentUser = user
  state.isLogin = true
}

export const setToken = (token: string): void => {
  state.token = token
}

export const setPermissions = (permissions: string[]): void => {
  state.permissions = permissions
}

export const logout = (): void => {
  state.currentUser = null
  state.isLogin = false
  state.token = null
  state.permissions = []
}

// 只读状态导出
export const userState = readonly(state)
```

### 2. 工具函数模块
```uts
// utils/format.uts - 格式化工具函数

// 日期格式化
export function formatDate(date: Date, format: string = 'YYYY-MM-DD'): string {
  const year = date.getFullYear()
  const month = (date.getMonth() + 1).toString().padStart(2, '0')
  const day = date.getDate().toString().padStart(2, '0')
  
  return format
    .replace('YYYY', year.toString())
    .replace('MM', month)
    .replace('DD', day)
}

// 数字格式化
export function formatNumber(num: number, decimals: number = 2): string {
  return num.toFixed(decimals)
}

// 文件大小格式化
export function formatFileSize(bytes: number): string {
  if (bytes === 0) return '0 B'
  
  const units = ['B', 'KB', 'MB', 'GB']
  const k = 1024
  const i = Math.floor(Math.log(bytes) / Math.log(k))
  
  return parseFloat((bytes / Math.pow(k, i)).toFixed(2)) + ' ' + units[i]
}

// 金额格式化
export function formatCurrency(amount: number, currency: string = '¥'): string {
  return currency + formatNumber(amount, 2)
}

// 字符串工具
export function capitalize(str: string): string {
  if (str.length === 0) return str
  return str.charAt(0).toUpperCase() + str.slice(1)
}

export function truncate(str: string, maxLength: number): string {
  if (str.length <= maxLength) return str
  return str.slice(0, maxLength) + '...'
}
```

### 3. API 服务模块
```uts
// services/api.uts - API 服务模块

export type ApiResponse<T> = {
  code: number,
  message: string,
  data: T
}

export type PaginationParams = {
  page: number,
  pageSize: number
}

export type ListResponse<T> = {
  items: T[],
  total: number,
  page: number,
  pageSize: number
}

// 基础API配置
const API_BASE_URL = 'https://api.example.com'
const REQUEST_TIMEOUT = 30000

// 请求拦截器
function createHeaders(token: string | null = null): UTSJSONObject {
  const headers: UTSJSONObject = {
    'Content-Type': 'application/json',
    'Accept': 'application/json'
  }
  
  if (token != null) {
    headers['Authorization'] = `Bearer ${token}`
  }
  
  return headers
}

// 通用请求方法
export async function request<T>(
  url: string,
  method: string = 'GET',
  data: UTSJSONObject | null = null,
  headers: UTSJSONObject | null = null
): Promise<ApiResponse<T>> {
  try {
    const requestOptions: RequestOptions = {
      url: `${API_BASE_URL}${url}`,
      method: method as RequestMethod,
      timeout: REQUEST_TIMEOUT,
      header: headers ?? createHeaders()
    }
    
    if (data != null && (method === 'POST' || method === 'PUT')) {
      requestOptions.data = data
    }
    
    const response = await uni.request(requestOptions)
    
    if (response.statusCode === 200) {
      return response.data as ApiResponse<T>
    } else {
      throw new Error(`请求失败: ${response.statusCode}`)
    }
  } catch (error) {
    console.error('API请求错误:', error)
    throw error
  }
}

// 具体API方法
export async function getUserInfo(userId: number): Promise<UserInfo> {
  const response = await request<UserInfo>(`/users/${userId}`)
  return response.data
}

export async function getUserList(params: PaginationParams): Promise<ListResponse<UserInfo>> {
  const response = await request<ListResponse<UserInfo>>(`/users?page=${params.page}&pageSize=${params.pageSize}`)
  return response.data
}

export async function createUser(userData: Partial<UserInfo>): Promise<UserInfo> {
  const response = await request<UserInfo>('/users', 'POST', userData as UTSJSONObject)
  return response.data
}
```

---

## 💾 状态管理最佳实践

### 1. 模块化状态管理
```uts
// store/index.uts - 根状态管理

import { reactive } from 'vue'
import type { UserState } from './user.uts'
import type { AppState } from './app.uts'

// 全局状态类型
export type GlobalState = {
  user: UserState,
  app: AppState
}

// 合并所有状态模块
export { state as userState, setUser, logout } from './user.uts'
export { state as appState, setTheme, setLanguage } from './app.uts'

// 全局配置状态
export const globalConfig = reactive({
  apiBaseUrl: 'https://api.example.com',
  version: '1.0.0',
  debug: false
})

// 全局方法
export const resetAllStates = (): void => {
  // 重置所有状态模块
  logout()
  setTheme('light')
  setLanguage('zh-CN')
}
```

### 2. 持久化状态管理
```uts
// store/persistent.uts - 持久化状态

export type PersistentState = {
  theme: string,
  language: string,
  userPreferences: UTSJSONObject
}

const STORAGE_KEY = 'app_persistent_state'

// 从存储加载状态
export function loadPersistedState(): PersistentState | null {
  try {
    const stored = uni.getStorageSync(STORAGE_KEY)
    if (stored != null) {
      return JSON.parse(stored as string) as PersistentState
    }
  } catch (error) {
    console.error('加载持久化状态失败:', error)
  }
  return null
}

// 保存状态到存储
export function savePersistedState(state: PersistentState): void {
  try {
    uni.setStorageSync(STORAGE_KEY, JSON.stringify(state))
  } catch (error) {
    console.error('保存持久化状态失败:', error)
  }
}

// 响应式持久化状态
export const persistentState = reactive<PersistentState>({
  theme: 'light',
  language: 'zh-CN',
  userPreferences: {}
})

// 初始化持久化状态
export const initPersistedState = (): void => {
  const loaded = loadPersistedState()
  if (loaded != null) {
    Object.assign(persistentState, loaded)
  }
}

// 更新并保存状态
export const updateTheme = (theme: string): void => {
  persistentState.theme = theme
  savePersistedState(persistentState)
}

export const updateLanguage = (language: string): void => {
  persistentState.language = language
  savePersistedState(persistentState)
}
```

---

## 🔧 工具函数开发规范

### 1. 验证工具函数
```uts
// utils/validation.uts - 验证工具

// 邮箱验证
export function isValidEmail(email: string): boolean {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  return emailRegex.test(email)
}

// 手机号验证
export function isValidPhone(phone: string): boolean {
  const phoneRegex = /^1[3-9]\d{9}$/
  return phoneRegex.test(phone)
}

// 身份证验证
export function isValidIdCard(idCard: string): boolean {
  const idCardRegex = /^[1-9]\d{5}(18|19|20)\d{2}((0[1-9])|(1[0-2]))(([0-2][1-9])|10|20|30|31)\d{3}[0-9Xx]$/
  return idCardRegex.test(idCard)
}

// 密码强度验证
export function validatePassword(password: string): {
  isValid: boolean,
  errors: string[]
} {
  const errors: string[] = []
  
  if (password.length < 8) {
    errors.push('密码长度至少8位')
  }
  
  if (!/[a-z]/.test(password)) {
    errors.push('密码必须包含小写字母')
  }
  
  if (!/[A-Z]/.test(password)) {
    errors.push('密码必须包含大写字母')
  }
  
  if (!/\d/.test(password)) {
    errors.push('密码必须包含数字')
  }
  
  return {
    isValid: errors.length === 0,
    errors
  }
}

// 表单验证器
export type ValidationRule = {
  required?: boolean,
  minLength?: number,
  maxLength?: number,
  pattern?: RegExp,
  custom?: (value: any) => boolean,
  message: string
}

export function validateField(value: any, rules: ValidationRule[]): string[] {
  const errors: string[] = []
  
  for (const rule of rules) {
    if (rule.required && (value == null || value === '')) {
      errors.push(rule.message)
      continue
    }
    
    if (rule.minLength != null && value.length < rule.minLength) {
      errors.push(rule.message)
      continue
    }
    
    if (rule.maxLength != null && value.length > rule.maxLength) {
      errors.push(rule.message)
      continue
    }
    
    if (rule.pattern != null && !rule.pattern.test(value)) {
      errors.push(rule.message)
      continue
    }
    
    if (rule.custom != null && !rule.custom(value)) {
      errors.push(rule.message)
    }
  }
  
  return errors
}
```

### 2. 异步工具函数
```uts
// utils/async.uts - 异步处理工具

// 延迟执行
export function delay(ms: number): Promise<void> {
  return new Promise((resolve) => {
    setTimeout(resolve, ms)
  })
}

// 重试机制
export async function retry<T>(
  fn: () => Promise<T>,
  maxAttempts: number = 3,
  delayMs: number = 1000
): Promise<T> {
  let lastError: Error
  
  for (let attempt = 1; attempt <= maxAttempts; attempt++) {
    try {
      return await fn()
    } catch (error) {
      lastError = error as Error
      
      if (attempt < maxAttempts) {
        await delay(delayMs)
        delayMs *= 2 // 指数退避
      }
    }
  }
  
  throw lastError!
}

// 超时控制
export function withTimeout<T>(
  promise: Promise<T>,
  timeoutMs: number
): Promise<T> {
  return new Promise((resolve, reject) => {
    const timer = setTimeout(() => {
      reject(new Error(`操作超时: ${timeoutMs}ms`))
    }, timeoutMs)
    
    promise
      .then(resolve)
      .catch(reject)
      .finally(() => clearTimeout(timer))
  })
}

// 并发控制
export async function concurrent<T>(
  tasks: Array<() => Promise<T>>,
  concurrency: number = 3
): Promise<T[]> {
  const results: T[] = []
  const executing: Promise<void>[] = []
  
  for (const task of tasks) {
    const promise = task().then(result => {
      results.push(result)
    })
    
    executing.push(promise)
    
    if (executing.length >= concurrency) {
      await Promise.race(executing)
      executing.splice(executing.findIndex(p => p === promise), 1)
    }
  }
  
  await Promise.all(executing)
  return results
}
```

### 3. 设备与平台工具
```uts
// utils/device.uts - 设备相关工具

export type DeviceInfo = {
  platform: string,
  system: string,
  version: string,
  screenWidth: number,
  screenHeight: number,
  pixelRatio: number
}

// 获取设备信息
export function getDeviceInfo(): DeviceInfo {
  const systemInfo = uni.getSystemInfoSync()
  
  return {
    platform: systemInfo.platform,
    system: systemInfo.system,
    version: systemInfo.version,
    screenWidth: systemInfo.screenWidth,
    screenHeight: systemInfo.screenHeight,
    pixelRatio: systemInfo.pixelRatio
  }
}

// 平台判断
export const isAndroid = (): boolean => {
  // #ifdef APP-ANDROID
  return true
  // #endif
  // #ifndef APP-ANDROID
  return false
  // #endif
}

export const isIOS = (): boolean => {
  // #ifdef APP-IOS
  return true
  // #endif
  // #ifndef APP-IOS
  return false
  // #endif
}

export const isWeb = (): boolean => {
  // #ifdef WEB
  return true
  // #endif
  // #ifndef WEB
  return false
  // #endif
}

// 屏幕适配
export function rpxToPx(rpx: number): number {
  const deviceInfo = getDeviceInfo()
  return rpx * deviceInfo.screenWidth / 750
}

export function pxToRpx(px: number): number {
  const deviceInfo = getDeviceInfo()
  return px * 750 / deviceInfo.screenWidth
}

// 安全区域
export function getSafeAreaInsets(): {
  top: number,
  bottom: number,
  left: number,
  right: number
} {
  const systemInfo = uni.getSystemInfoSync()
  const safeArea = systemInfo.safeArea
  
  return {
    top: safeArea?.top ?? 0,
    bottom: systemInfo.screenHeight - (safeArea?.bottom ?? systemInfo.screenHeight),
    left: safeArea?.left ?? 0,
    right: systemInfo.screenWidth - (safeArea?.right ?? systemInfo.screenWidth)
  }
}
```

---

## 🔄 响应式组合函数

### 1. 基础组合函数
```uts
// composables/useCounter.uts - 计数器组合函数

import { ref, computed, readonly } from 'vue'

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
  
  const isZero = computed((): boolean => {
    return count.value === 0
  })
  
  const isPositive = computed((): boolean => {
    return count.value > 0
  })
  
  const isNegative = computed((): boolean => {
    return count.value < 0
  })
  
  return {
    count: readonly(count),
    increment,
    decrement,
    reset,
    isZero,
    isPositive,
    isNegative
  }
}
```

### 2. 表单处理组合函数
```uts
// composables/useForm.uts - 表单处理组合函数

import { reactive, computed } from 'vue'
import type { ValidationRule } from '../utils/validation.uts'
import { validateField } from '../utils/validation.uts'

export type FormField<T> = {
  value: T,
  rules: ValidationRule[],
  errors: string[],
  touched: boolean
}

export type FormState = {
  [key: string]: FormField<any>
}

export function useForm<T extends FormState>(initialState: T) {
  const form = reactive(initialState)
  
  // 验证单个字段
  const validateFieldFn = (fieldName: string): void => {
    const field = form[fieldName]
    if (field != null) {
      field.errors = validateField(field.value, field.rules)
      field.touched = true
    }
  }
  
  // 验证所有字段
  const validateAll = (): boolean => {
    let isValid = true
    
    for (const fieldName in form) {
      validateFieldFn(fieldName)
      if (form[fieldName].errors.length > 0) {
        isValid = false
      }
    }
    
    return isValid
  }
  
  // 重置表单
  const reset = (): void => {
    for (const fieldName in form) {
      const field = form[fieldName]
      field.errors = []
      field.touched = false
      // 重置值需要根据类型处理
      if (typeof field.value === 'string') {
        field.value = ''
      } else if (typeof field.value === 'number') {
        field.value = 0
      } else if (typeof field.value === 'boolean') {
        field.value = false
      }
    }
  }
  
  // 计算属性
  const isValid = computed((): boolean => {
    for (const fieldName in form) {
      if (form[fieldName].errors.length > 0) {
        return false
      }
    }
    return true
  })
  
  const hasErrors = computed((): boolean => {
    return !isValid.value
  })
  
  const touchedFields = computed((): string[] => {
    return Object.keys(form).filter(key => form[key].touched)
  })
  
  return {
    form,
    validateField: validateFieldFn,
    validateAll,
    reset,
    isValid,
    hasErrors,
    touchedFields
  }
}
```

### 3. 网络请求组合函数
```uts
// composables/useRequest.uts - 网络请求组合函数

import { ref, computed } from 'vue'

export type RequestState<T> = {
  data: T | null,
  loading: boolean,
  error: Error | null
}

export function useRequest<T>() {
  const data = ref<T | null>(null)
  const loading = ref(false)
  const error = ref<Error | null>(null)
  
  const execute = async (requestFn: () => Promise<T>): Promise<T | null> => {
    try {
      loading.value = true
      error.value = null
      
      const result = await requestFn()
      data.value = result
      return result
    } catch (err) {
      error.value = err as Error
      return null
    } finally {
      loading.value = false
    }
  }
  
  const reset = (): void => {
    data.value = null
    loading.value = false
    error.value = null
  }
  
  const isSuccess = computed((): boolean => {
    return data.value != null && error.value == null
  })
  
  const isError = computed((): boolean => {
    return error.value != null
  })
  
  return {
    data: readonly(data),
    loading: readonly(loading),
    error: readonly(error),
    execute,
    reset,
    isSuccess,
    isError
  }
}

// 使用示例
export function useUserRequest() {
  const { data, loading, error, execute, reset } = useRequest<UserInfo>()
  
  const fetchUser = async (userId: number): Promise<UserInfo | null> => {
    return await execute(async () => {
      const response = await getUserInfo(userId)
      return response
    })
  }
  
  return {
    userData: data,
    userLoading: loading,
    userError: error,
    fetchUser,
    resetUser: reset
  }
}
```

---

## 🛡️ 错误处理与调试

### 1. 错误处理工具
```uts
// utils/error.uts - 错误处理

export class AppError extends Error {
  public code: string
  public statusCode: number
  
  constructor(message: string, code: string = 'UNKNOWN_ERROR', statusCode: number = 500) {
    super(message)
    this.name = 'AppError'
    this.code = code
    this.statusCode = statusCode
  }
}

export class ValidationError extends AppError {
  constructor(message: string) {
    super(message, 'VALIDATION_ERROR', 400)
    this.name = 'ValidationError'
  }
}

export class NetworkError extends AppError {
  constructor(message: string) {
    super(message, 'NETWORK_ERROR', 500)
    this.name = 'NetworkError'
  }
}

// 全局错误处理器
export const handleError = (error: Error, context: string = ''): void => {
  console.error(`[${context}] 错误:`, error)
  
  // 根据错误类型进行不同处理
  if (error instanceof ValidationError) {
    uni.showToast({
      title: error.message,
      icon: 'none'
    })
  } else if (error instanceof NetworkError) {
    uni.showToast({
      title: '网络请求失败，请稍后重试',
      icon: 'none'
    })
  } else {
    uni.showToast({
      title: '操作失败，请重试',
      icon: 'none'
    })
  }
}

// 错误边界包装器
export function withErrorHandling<T extends any[], R>(
  fn: (...args: T) => Promise<R>,
  context: string = ''
) {
  return async (...args: T): Promise<R | null> => {
    try {
      return await fn(...args)
    } catch (error) {
      handleError(error as Error, context)
      return null
    }
  }
}
```

### 2. 调试工具
```uts
// utils/debug.uts - 调试工具

export const DEBUG = true // 可以通过构建配置控制

export const logger = {
  log: (message: string, ...args: any[]): void => {
    if (DEBUG) {
      console.log(`[LOG] ${message}`, ...args)
    }
  },
  
  warn: (message: string, ...args: any[]): void => {
    if (DEBUG) {
      console.warn(`[WARN] ${message}`, ...args)
    }
  },
  
  error: (message: string, ...args: any[]): void => {
    console.error(`[ERROR] ${message}`, ...args)
  },
  
  debug: (message: string, ...args: any[]): void => {
    if (DEBUG) {
      console.debug(`[DEBUG] ${message}`, ...args)
    }
  }
}

// 性能监测
export function performance(label: string) {
  const startTime = Date.now()
  
  return {
    end: (): void => {
      const endTime = Date.now()
      logger.debug(`性能监测 [${label}]: ${endTime - startTime}ms`)
    }
  }
}

// 函数执行时间监测
export function measureTime<T extends any[], R>(
  fn: (...args: T) => R,
  label: string = ''
) {
  return (...args: T): R => {
    const perf = performance(label || fn.name)
    const result = fn(...args)
    perf.end()
    return result
  }
}
```

---

## 📋 开发检查清单

### 模块结构检查
- [ ] 类型定义放在文件顶部
- [ ] 使用明确的导入导出
- [ ] 避免循环依赖
- [ ] 使用描述性的文件和函数命名

### 状态管理检查
- [ ] 使用 `reactive()` 创建状态对象
- [ ] 提供类型化的状态更新方法
- [ ] 使用 `readonly()` 暴露只读状态
- [ ] 考虑状态持久化需求

### 工具函数检查
- [ ] 函数功能单一，职责明确
- [ ] 包含完整的类型注解
- [ ] 提供适当的错误处理
- [ ] 编写对应的使用示例

### 错误处理检查
- [ ] 使用自定义错误类型
- [ ] 提供统一的错误处理机制
- [ ] 包含适当的用户提示
- [ ] 记录详细的错误日志

### 性能优化检查
- [ ] 避免在状态中存储大量数据
- [ ] 合理使用计算属性缓存
- [ ] 异步操作包含加载状态
- [ ] 及时清理不需要的资源

此指南涵盖了 UTS 模块开发的核心要点，有助于构建可维护、高性能的 uni-app x 应用程序。

````
