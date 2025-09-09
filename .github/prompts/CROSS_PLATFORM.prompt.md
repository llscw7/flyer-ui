---
mode: agent
---
# UNI-APP X 跨端开发最佳实践

基于 uni-app x 的跨端开发指南，涵盖平台差异处理、条件编译、API适配、性能优化等关键要素。

## 🎯 平台差异与条件编译

### 1. 条件编译语法
```uvue
<!-- 模板中的条件编译 -->
<template>
  <!-- 仅 App 平台显示 -->
  <!-- #ifdef APP -->
  <scroll-view style="flex: 1">
    <view>App 专有内容</view>
  </scroll-view>
  <!-- #endif -->
  
  <!-- 仅 Android 平台 -->
  <!-- #ifdef APP-ANDROID -->
  <view>Android 专有功能</view>
  <!-- #endif -->
  
  <!-- 仅 iOS 平台 -->
  <!-- #ifdef APP-IOS -->
  <view>iOS 专有功能</view>
  <!-- #endif -->
  
  <!-- 仅 Web 平台 -->
  <!-- #ifdef WEB -->
  <view>Web 专有内容</view>
  <!-- #endif -->
  
  <!-- 排除某平台 -->
  <!-- #ifndef APP-IOS -->
  <view>非 iOS 平台显示</view>
  <!-- #endif -->
  
  <!-- 多平台组合 -->
  <!-- #ifdef APP-ANDROID || APP-IOS -->
  <view>移动端通用内容</view>
  <!-- #endif -->
</template>

<script setup lang="uts">
// 脚本中的条件编译
// #ifdef APP-ANDROID
const isAndroid = true
console.log('Android 环境')
// #endif

// #ifdef APP-IOS  
const isIOS = true
console.log('iOS 环境')
// #endif

// #ifdef WEB
const isWeb = true
console.log('Web 环境')
// #endif

// #ifndef APP
const isMiniProgram = true
console.log('小程序环境')
// #endif

// 平台判断函数
function getPlatform(): string {
  // #ifdef APP-ANDROID
  return 'android'
  // #endif
  
  // #ifdef APP-IOS
  return 'ios'  
  // #endif
  
  // #ifdef WEB
  return 'web'
  // #endif
  
  // #ifndef APP
  return 'miniprogram'
  // #endif
}
</script>

<style>
/* 样式中的条件编译 */
.container {
  padding: 20px;
}

/* #ifdef APP */
.container {
  padding-top: 44px; /* App 状态栏高度 */
}
/* #endif */

/* #ifdef WEB */
.container {
  max-width: 750px;
  margin: 0 auto;
}
/* #endif */
</style>
```

### 2. 平台特有功能封装
```uts
// utils/platform.uts - 平台工具函数

export type PlatformInfo = {
  platform: string,
  isApp: boolean,
  isAndroid: boolean,
  isIOS: boolean,
  isWeb: boolean,
  isMiniProgram: boolean
}

export function getPlatformInfo(): PlatformInfo {
  let platform = 'unknown'
  let isApp = false
  let isAndroid = false
  let isIOS = false
  let isWeb = false
  let isMiniProgram = false
  
  // #ifdef APP-ANDROID
  platform = 'android'
  isApp = true
  isAndroid = true
  // #endif
  
  // #ifdef APP-IOS
  platform = 'ios'
  isApp = true
  isIOS = true
  // #endif
  
  // #ifdef WEB
  platform = 'web'
  isWeb = true
  // #endif
  
  // #ifndef APP
  // #ifndef WEB
  platform = 'miniprogram'
  isMiniProgram = true
  // #endif
  // #endif
  
  return {
    platform,
    isApp,
    isAndroid,
    isIOS,
    isWeb,
    isMiniProgram
  }
}

// 状态栏高度获取
export function getStatusBarHeight(): number {
  let statusBarHeight = 0
  
  // #ifdef APP
  const systemInfo = uni.getSystemInfoSync()
  statusBarHeight = systemInfo.statusBarHeight || 0
  // #endif
  
  // #ifdef WEB
  statusBarHeight = 0 // Web 端没有状态栏
  // #endif
  
  return statusBarHeight
}

// 安全区域获取
export function getSafeAreaInsets(): {
  top: number,
  bottom: number,
  left: number,
  right: number
} {
  let insets = { top: 0, bottom: 0, left: 0, right: 0 }
  
  // #ifdef APP
  const systemInfo = uni.getSystemInfoSync()
  if (systemInfo.safeArea) {
    const screenHeight = systemInfo.screenHeight
    const screenWidth = systemInfo.screenWidth
    const safeArea = systemInfo.safeArea
    
    insets = {
      top: safeArea.top,
      bottom: screenHeight - safeArea.bottom,
      left: safeArea.left,
      right: screenWidth - safeArea.right
    }
  }
  // #endif
  
  return insets
}
```

---

## 📱 API 适配与统一封装

### 1. 网络请求统一封装
```uts
// services/request.uts - 请求统一封装

export type RequestConfig = {
  url: string,
  method?: 'GET' | 'POST' | 'PUT' | 'DELETE',
  data?: any,
  header?: UTSJSONObject,
  timeout?: number
}

export type ResponseData<T> = {
  code: number,
  message: string,
  data: T
}

class HttpClient {
  private baseURL: string = ''
  private timeout: number = 30000
  private defaultHeaders: UTSJSONObject = {}
  
  constructor(baseURL: string = '') {
    this.baseURL = baseURL
    this.initDefaultHeaders()
  }
  
  private initDefaultHeaders(): void {
    this.defaultHeaders = {
      'Content-Type': 'application/json'
    }
    
    // 平台特定头部
    // #ifdef APP
    this.defaultHeaders['X-Platform'] = 'app'
    // #endif
    
    // #ifdef WEB
    this.defaultHeaders['X-Platform'] = 'web'
    // #endif
  }
  
  private buildURL(url: string): string {
    if (url.startsWith('http')) {
      return url
    }
    return this.baseURL + url
  }
  
  private async handleRequest<T>(config: RequestConfig): Promise<ResponseData<T>> {
    const requestConfig: RequestOptions = {
      url: this.buildURL(config.url),
      method: config.method as RequestMethod || 'GET',
      timeout: config.timeout || this.timeout,
      header: { ...this.defaultHeaders, ...config.header }
    }
    
    if (config.data && (config.method === 'POST' || config.method === 'PUT')) {
      requestConfig.data = config.data
    }
    
    try {
      const response = await uni.request(requestConfig)
      
      if (response.statusCode >= 200 && response.statusCode < 300) {
        return response.data as ResponseData<T>
      } else {
        throw new Error(`HTTP ${response.statusCode}: ${response.errMsg}`)
      }
    } catch (error) {
      console.error('Request failed:', error)
      throw error
    }
  }
  
  async get<T>(url: string, config?: Partial<RequestConfig>): Promise<ResponseData<T>> {
    return this.handleRequest<T>({
      url,
      method: 'GET',
      ...config
    })
  }
  
  async post<T>(url: string, data?: any, config?: Partial<RequestConfig>): Promise<ResponseData<T>> {
    return this.handleRequest<T>({
      url,
      method: 'POST',
      data,
      ...config
    })
  }
  
  async put<T>(url: string, data?: any, config?: Partial<RequestConfig>): Promise<ResponseData<T>> {
    return this.handleRequest<T>({
      url,
      method: 'PUT',
      data,
      ...config
    })
  }
  
  async delete<T>(url: string, config?: Partial<RequestConfig>): Promise<ResponseData<T>> {
    return this.handleRequest<T>({
      url,
      method: 'DELETE',
      ...config
    })
  }
}

// 创建默认实例
export const http = new HttpClient('https://api.example.com')
```

### 2. 存储统一封装
```uts
// utils/storage.uts - 存储统一封装

export class StorageManager {
  // 同步存储
  static setSync(key: string, value: any): boolean {
    try {
      const data = JSON.stringify(value)
      uni.setStorageSync(key, data)
      return true
    } catch (error) {
      console.error('Storage setSync failed:', error)
      return false
    }
  }
  
  static getSync<T>(key: string, defaultValue: T | null = null): T | null {
    try {
      const data = uni.getStorageSync(key)
      if (data != null && data !== '') {
        return JSON.parse(data as string) as T
      }
      return defaultValue
    } catch (error) {
      console.error('Storage getSync failed:', error)
      return defaultValue
    }
  }
  
  static removeSync(key: string): boolean {
    try {
      uni.removeStorageSync(key)
      return true
    } catch (error) {
      console.error('Storage removeSync failed:', error)
      return false
    }
  }
  
  static clearSync(): boolean {
    try {
      uni.clearStorageSync()
      return true
    } catch (error) {
      console.error('Storage clearSync failed:', error)
      return false
    }
  }
  
  // 异步存储
  static async set(key: string, value: any): Promise<boolean> {
    try {
      const data = JSON.stringify(value)
      await uni.setStorage({
        key,
        data
      })
      return true
    } catch (error) {
      console.error('Storage set failed:', error)
      return false
    }
  }
  
  static async get<T>(key: string, defaultValue: T | null = null): Promise<T | null> {
    try {
      const result = await uni.getStorage({ key })
      if (result.data != null) {
        return JSON.parse(result.data as string) as T
      }
      return defaultValue
    } catch (error) {
      console.error('Storage get failed:', error)
      return defaultValue
    }
  }
  
  static async remove(key: string): Promise<boolean> {
    try {
      await uni.removeStorage({ key })
      return true
    } catch (error) {
      console.error('Storage remove failed:', error)
      return false
    }
  }
  
  // 获取存储信息
  static async getInfo(): Promise<{
    keys: string[],
    currentSize: number,
    limitSize: number
  }> {
    try {
      const info = await uni.getStorageInfo()
      return {
        keys: info.keys as string[],
        currentSize: info.currentSize as number,
        limitSize: info.limitSize as number
      }
    } catch (error) {
      console.error('Get storage info failed:', error)
      return {
        keys: [],
        currentSize: 0,
        limitSize: 0
      }
    }
  }
}

// 便捷方法
export const storage = {
  set: StorageManager.setSync,
  get: StorageManager.getSync,
  remove: StorageManager.removeSync,
  clear: StorageManager.clearSync,
  async: {
    set: StorageManager.set,
    get: StorageManager.get,
    remove: StorageManager.remove
  }
}
```

### 3. 导航统一封装
```uts
// utils/navigation.uts - 导航统一封装

export type NavigateOptions = {
  url: string,
  params?: UTSJSONObject,
  animationType?: string,
  animationDuration?: number
}

export class Navigation {
  // 构建URL参数
  private static buildURL(url: string, params?: UTSJSONObject): string {
    if (!params) return url
    
    const queryString = Object.keys(params)
      .map(key => `${key}=${encodeURIComponent(params[key] as string)}`)
      .join('&')
    
    return url + (url.includes('?') ? '&' : '?') + queryString
  }
  
  // 保留当前页面，跳转到新页面
  static async navigateTo(options: NavigateOptions): Promise<void> {
    try {
      const url = this.buildURL(options.url, options.params)
      
      await uni.navigateTo({
        url,
        animationType: options.animationType,
        animationDuration: options.animationDuration
      })
    } catch (error) {
      console.error('NavigateTo failed:', error)
      throw error
    }
  }
  
  // 关闭当前页面，跳转到新页面
  static async redirectTo(options: NavigateOptions): Promise<void> {
    try {
      const url = this.buildURL(options.url, options.params)
      
      await uni.redirectTo({ url })
    } catch (error) {
      console.error('RedirectTo failed:', error)
      throw error
    }
  }
  
  // 跳转到 tabBar 页面
  static async switchTab(url: string): Promise<void> {
    try {
      await uni.switchTab({ url })
    } catch (error) {
      console.error('SwitchTab failed:', error)
      throw error
    }
  }
  
  // 关闭所有页面，打开到应用内的某个页面
  static async reLaunch(options: NavigateOptions): Promise<void> {
    try {
      const url = this.buildURL(options.url, options.params)
      
      await uni.reLaunch({ url })
    } catch (error) {
      console.error('ReLaunch failed:', error)
      throw error
    }
  }
  
  // 关闭当前页面，返回上一页面或多级页面
  static async navigateBack(delta: number = 1): Promise<void> {
    try {
      await uni.navigateBack({ delta })
    } catch (error) {
      console.error('NavigateBack failed:', error)
      throw error
    }
  }
  
  // 预加载页面
  static async preloadPage(url: string): Promise<void> {
    // #ifdef APP
    try {
      await uni.preloadPage({ url })
    } catch (error) {
      console.error('PreloadPage failed:', error)
    }
    // #endif
    
    // #ifndef APP
    console.log('PreloadPage is only supported on App platform')
    // #endif
  }
}

// 便捷导出
export const nav = Navigation
```

---

## 🎨 样式与布局适配

### 1. 响应式布局
```uvue
<template>
  <view class="responsive-container">
    <view class="header">
      <text class="title">响应式标题</text>
    </view>
    
    <view class="content" :class="contentClass">
      <view class="sidebar" v-if="showSidebar">
        <text>侧边栏</text>
      </view>
      
      <view class="main">
        <text>主要内容</text>
      </view>
    </view>
  </view>
</template>

<script setup lang="uts">
import { ref, computed, onMounted } from 'vue'

const screenWidth = ref(0)
const screenHeight = ref(0)

// 获取屏幕尺寸
onMounted(() => {
  const systemInfo = uni.getSystemInfoSync()
  screenWidth.value = systemInfo.screenWidth
  screenHeight.value = systemInfo.screenHeight
})

// 响应式计算
const isTablet = computed((): boolean => {
  return screenWidth.value >= 768
})

const isMobile = computed((): boolean => {
  return screenWidth.value < 768
})

const showSidebar = computed((): boolean => {
  return isTablet.value
})

const contentClass = computed((): string => {
  return isTablet.value ? 'content--tablet' : 'content--mobile'
})
</script>

<style>
.responsive-container {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.header {
  padding: 20px;
  background-color: #f8f8f8;
  border-bottom: 1px solid #e0e0e0;
}

.title {
  font-size: 18px;
  font-weight: bold;
}

.content {
  flex: 1;
  display: flex;
  flex-direction: row;
}

.content--mobile {
  flex-direction: column;
}

.content--tablet {
  flex-direction: row;
}

.sidebar {
  width: 250px;
  background-color: #f0f0f0;
  padding: 20px;
}

.main {
  flex: 1;
  padding: 20px;
}

/* 平台特定样式 */
/* #ifdef APP */
.header {
  padding-top: var(--status-bar-height);
}
/* #endif */

/* #ifdef WEB */
.responsive-container {
  max-width: 1200px;
  margin: 0 auto;
}
/* #endif */
</style>
```

### 2. 安全区域适配
```uvue
<template>
  <view class="safe-area-container">
    <view class="header" :style="headerStyle">
      <text>头部内容</text>
    </view>
    
    <view class="content">
      <text>主要内容</text>
    </view>
    
    <view class="footer" :style="footerStyle">
      <text>底部内容</text>
    </view>
  </view>
</template>

<script setup lang="uts">
import { computed, onMounted, ref } from 'vue'

const safeAreaInsets = ref({
  top: 0,
  bottom: 0,
  left: 0,
  right: 0
})

onMounted(() => {
  // #ifdef APP
  const systemInfo = uni.getSystemInfoSync()
  if (systemInfo.safeArea) {
    safeAreaInsets.value = {
      top: systemInfo.safeArea.top,
      bottom: systemInfo.screenHeight - systemInfo.safeArea.bottom,
      left: systemInfo.safeArea.left,
      right: systemInfo.screenWidth - systemInfo.safeArea.right
    }
  }
  // #endif
})

const headerStyle = computed(() => {
  return {
    paddingTop: `${safeAreaInsets.value.top}px`
  }
})

const footerStyle = computed(() => {
  return {
    paddingBottom: `${safeAreaInsets.value.bottom}px`
  }
})
</script>

<style>
.safe-area-container {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.header {
  background-color: #007aff;
  color: white;
  padding: 20px;
}

.content {
  flex: 1;
  padding: 20px;
}

.footer {
  background-color: #f8f8f8;
  padding: 20px;
  border-top: 1px solid #e0e0e0;
}
</style>
```

---

## ⚡ 性能优化策略

### 1. 代码分割与懒加载
```uts
// pages/index/index.uvue - 主页面

<template>
  <view class="home-page">
    <!-- 核心内容立即渲染 -->
    <view class="header">
      <text>{{ title }}</text>
    </view>
    
    <!-- 次要内容懒加载 -->
    <lazy-component v-if="showLazyContent" />
    
    <!-- 条件渲染重要内容 -->
    <heavy-component v-if="shouldLoadHeavyComponent" />
  </view>
</template>

<script setup lang="uts">
import { ref, onMounted, nextTick } from 'vue'

// 懒加载组件
const LazyComponent = defineAsyncComponent(() => 
  import('../../components/LazyComponent.uvue')
)

const HeavyComponent = defineAsyncComponent(() =>
  import('../../components/HeavyComponent.uvue')
)

const title = ref('首页')
const showLazyContent = ref(false)
const shouldLoadHeavyComponent = ref(false)

onMounted(async () => {
  // 页面渲染完成后加载次要内容
  await nextTick()
  
  setTimeout(() => {
    showLazyContent.value = true
  }, 100)
  
  // 根据条件决定是否加载重组件
  setTimeout(() => {
    const needHeavyFeature = checkHeavyFeatureNeed()
    if (needHeavyFeature) {
      shouldLoadHeavyComponent.value = true
    }
  }, 500)
})

function checkHeavyFeatureNeed(): boolean {
  // 检查是否需要重要功能
  return true
}
</script>
```

### 2. 图片优化
```uts
// components/OptimizedImage.uvue - 优化的图片组件

<template>
  <view class="image-container">
    <!-- 占位符 -->
    <view v-if="loading" class="placeholder" :style="placeholderStyle">
      <text class="loading-text">加载中...</text>
    </view>
    
    <!-- 实际图片 -->
    <image 
      v-show="!loading"
      :src="optimizedSrc"
      :mode="mode"
      :lazy-load="lazyLoad"
      @load="handleImageLoad"
      @error="handleImageError"
      class="optimized-image"
      :style="imageStyle"
    />
    
    <!-- 错误状态 -->
    <view v-if="error" class="error-placeholder" :style="placeholderStyle">
      <text class="error-text">加载失败</text>
    </view>
  </view>
</template>

<script setup lang="uts">
type Props = {
  src: string,
  width?: number,
  height?: number,
  mode?: string,
  lazyLoad?: boolean,
  quality?: number
}

const props = withDefaults(defineProps<Props>(), {
  width: 200,
  height: 200,
  mode: 'aspectFill',
  lazyLoad: true,
  quality: 80
})

const loading = ref(true)
const error = ref(false)

// 图片尺寸优化
const optimizedSrc = computed((): string => {
  let src = props.src
  
  // #ifdef APP
  // App 端可以添加尺寸参数优化
  if (src.includes('?')) {
    src += `&w=${props.width}&h=${props.height}&q=${props.quality}`
  } else {
    src += `?w=${props.width}&h=${props.height}&q=${props.quality}`
  }
  // #endif
  
  return src
})

const placeholderStyle = computed(() => ({
  width: `${props.width}px`,
  height: `${props.height}px`
}))

const imageStyle = computed(() => ({
  width: `${props.width}px`,
  height: `${props.height}px`
}))

function handleImageLoad(): void {
  loading.value = false
  error.value = false
}

function handleImageError(): void {
  loading.value = false
  error.value = true
}
</script>

<style>
.image-container {
  position: relative;
}

.placeholder, .error-placeholder {
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #f5f5f5;
}

.loading-text, .error-text {
  font-size: 12px;
  color: #999;
}

.optimized-image {
  display: block;
}
</style>
```

### 3. 内存管理
```uts
// utils/memory.uts - 内存管理工具

export class MemoryManager {
  private static timers: Map<string, number> = new Map()
  private static intervals: Map<string, number> = new Map()
  private static listeners: Map<string, Function> = new Map()
  
  // 定时器管理
  static setTimeout(callback: Function, delay: number, id?: string): string {
    const timerId = id || `timer_${Date.now()}_${Math.random()}`
    const timer = setTimeout(() => {
      callback()
      this.timers.delete(timerId)
    }, delay)
    
    this.timers.set(timerId, timer)
    return timerId
  }
  
  static clearTimeout(id: string): void {
    const timer = this.timers.get(id)
    if (timer) {
      clearTimeout(timer)
      this.timers.delete(id)
    }
  }
  
  static setInterval(callback: Function, delay: number, id?: string): string {
    const intervalId = id || `interval_${Date.now()}_${Math.random()}`
    const interval = setInterval(callback, delay)
    
    this.intervals.set(intervalId, interval)
    return intervalId
  }
  
  static clearInterval(id: string): void {
    const interval = this.intervals.get(id)
    if (interval) {
      clearInterval(interval)
      this.intervals.delete(id)
    }
  }
  
  // 清理所有定时器
  static clearAllTimers(): void {
    this.timers.forEach((timer) => clearTimeout(timer))
    this.timers.clear()
    
    this.intervals.forEach((interval) => clearInterval(interval))
    this.intervals.clear()
  }
  
  // 事件监听器管理
  static addEventListener(element: any, event: string, handler: Function, id?: string): string {
    const listenerId = id || `listener_${Date.now()}_${Math.random()}`
    
    element.addEventListener(event, handler)
    this.listeners.set(listenerId, () => {
      element.removeEventListener(event, handler)
    })
    
    return listenerId
  }
  
  static removeEventListener(id: string): void {
    const remover = this.listeners.get(id)
    if (remover) {
      remover()
      this.listeners.delete(id)
    }
  }
  
  // 清理所有监听器
  static clearAllListeners(): void {
    this.listeners.forEach((remover) => remover())
    this.listeners.clear()
  }
  
  // 获取内存使用信息
  static getMemoryInfo(): Promise<any> {
    // #ifdef APP
    return new Promise((resolve) => {
      uni.getPerformance({
        success: (res) => {
          resolve(res)
        },
        fail: () => {
          resolve(null)
        }
      })
    })
    // #endif
    
    // #ifndef APP
    return Promise.resolve(null)
    // #endif
  }
}

// 组合函数：自动清理资源
export function useAutoCleanup() {
  const timers: string[] = []
  const intervals: string[] = []
  const listeners: string[] = []
  
  const addTimer = (callback: Function, delay: number): string => {
    const id = MemoryManager.setTimeout(callback, delay)
    timers.push(id)
    return id
  }
  
  const addInterval = (callback: Function, delay: number): string => {
    const id = MemoryManager.setInterval(callback, delay)
    intervals.push(id)
    return id
  }
  
  const cleanup = (): void => {
    timers.forEach(id => MemoryManager.clearTimeout(id))
    intervals.forEach(id => MemoryManager.clearInterval(id))
    listeners.forEach(id => MemoryManager.removeEventListener(id))
    
    timers.length = 0
    intervals.length = 0
    listeners.length = 0
  }
  
  onUnmounted(() => {
    cleanup()
  })
  
  return {
    addTimer,
    addInterval,
    cleanup
  }
}
```

---

## 📋 跨端开发检查清单

### 平台适配检查
- [ ] 使用条件编译处理平台差异
- [ ] 平台特有功能提供降级方案
- [ ] API 调用前检查平台支持性
- [ ] 样式适配不同屏幕尺寸

### API 封装检查
- [ ] 网络请求统一错误处理
- [ ] 存储操作异常捕获
- [ ] 导航跳转参数验证
- [ ] 平台 API 差异抹平

### 性能优化检查
- [ ] 图片懒加载和尺寸优化
- [ ] 组件懒加载和代码分割
- [ ] 内存泄漏防护
- [ ] 定时器和监听器清理

### 用户体验检查
- [ ] 加载状态和错误提示
- [ ] 安全区域适配
- [ ] 响应式布局
- [ ] 平台特有交互优化

### 测试验证检查
- [ ] 多平台功能测试
- [ ] 性能压力测试
- [ ] 内存使用监控
- [ ] 用户体验评估

此指南涵盖了 uni-app x 跨端开发的关键要点，有助于构建高质量、一致性强的跨平台应用。

````
