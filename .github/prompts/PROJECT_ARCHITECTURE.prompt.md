---
mode: agent
---
# UNI-APP X 项目架构与开发流程指南

基于 uni-app x 框架的完整项目开发指南，涵盖项目结构、开发流程、代码规范、测试部署等全生命周期。

## 🏗️ 项目架构设计

### 1. 标准项目结构
```
project-root/
├── src/                          # 源码目录
│   ├── components/              # 通用组件
│   │   ├── base/               # 基础组件（Button、Input等）
│   │   ├── business/           # 业务组件
│   │   └── layout/             # 布局组件
│   ├── pages/                  # 页面文件
│   │   ├── home/              
│   │   │   └── index.uvue
│   │   └── user/
│   │       ├── profile.uvue
│   │       └── settings.uvue
│   ├── store/                  # 状态管理
│   │   ├── index.uts          # 根状态
│   │   ├── modules/           # 状态模块
│   │   │   ├── user.uts
│   │   │   └── app.uts
│   │   └── types.uts          # 状态类型定义
│   ├── services/              # API服务层
│   │   ├── api/              # API接口
│   │   ├── http.uts          # HTTP客户端
│   │   └── types.uts         # 服务类型
│   ├── utils/                # 工具函数
│   │   ├── common.uts        # 通用工具
│   │   ├── validation.uts    # 验证工具
│   │   ├── format.uts        # 格式化工具
│   │   └── platform.uts      # 平台工具
│   ├── composables/          # 组合函数
│   │   ├── useRequest.uts    # 请求组合函数
│   │   ├── useStorage.uts    # 存储组合函数
│   │   └── useAuth.uts       # 认证组合函数
│   ├── types/                # 全局类型定义
│   │   ├── common.uts        # 通用类型
│   │   ├── api.uts           # API类型
│   │   └── components.uts    # 组件类型
│   ├── styles/               # 样式文件
│   │   ├── common.css        # 通用样式
│   │   ├── variables.css     # CSS变量
│   │   └── themes/           # 主题样式
│   └── static/               # 静态资源
│       ├── images/           # 图片资源
│       ├── icons/            # 图标资源
│       └── fonts/            # 字体资源
├── .github/                  # GitHub配置
│   └── prompts/             # 开发提示文件
├── docs/                     # 项目文档
├── tests/                    # 测试文件
├── App.uvue                  # 应用主组件
├── main.uts                  # 应用入口
├── manifest.json            # 应用配置
├── pages.json               # 页面配置
├── uni.scss                 # 全局样式
└── package.json             # 项目依赖
```

### 2. 配置文件详解
```json
// manifest.json - 应用配置
{
  "name": "项目名称",
  "appid": "__UNI__XXXXXX",
  "description": "项目描述",
  "versionName": "1.0.0",
  "versionCode": "100",
  "transformPx": false,
  "app": {
    "splashscreen": {
      "alwaysShowBeforeRender": true,
      "waiting": true,
      "autoclose": true,
      "delay": 0
    },
    "modules": {},
    "distribute": {
      "android": {
        "permissions": [
          "<uses-permission android:name=\"android.permission.CHANGE_NETWORK_STATE\" />",
          "<uses-permission android:name=\"android.permission.MOUNT_UNMOUNT_FILESYSTEMS\" />",
          "<uses-permission android:name=\"android.permission.VIBRATE\" />",
          "<uses-permission android:name=\"android.permission.READ_LOGS\" />",
          "<uses-permission android:name=\"android.permission.ACCESS_WIFI_STATE\" />",
          "<uses-feature android:name=\"android.hardware.camera.autofocus\" />",
          "<uses-permission android:name=\"android.permission.ACCESS_NETWORK_STATE\" />",
          "<uses-permission android:name=\"android.permission.CAMERA\" />",
          "<uses-permission android:name=\"android.permission.GET_ACCOUNTS\" />",
          "<uses-permission android:name=\"android.permission.READ_PHONE_STATE\" />",
          "<uses-permission android:name=\"android.permission.CHANGE_WIFI_STATE\" />",
          "<uses-permission android:name=\"android.permission.WAKE_LOCK\" />",
          "<uses-permission android:name=\"android.permission.FLASHLIGHT\" />",
          "<uses-permission android:name=\"android.permission.WRITE_SETTINGS\" />"
        ]
      },
      "ios": {
        "idfa": false
      }
    }
  },
  "quickapp": {},
  "mp-weixin": {
    "appid": "",
    "setting": {
      "urlCheck": false
    },
    "usingComponents": true
  },
  "mp-alipay": {
    "usingComponents": true
  },
  "mp-baidu": {
    "usingComponents": true
  },
  "mp-toutiao": {
    "usingComponents": true
  },
  "uniStatistics": {
    "enable": false
  },
  "vueVersion": "3"
}
```

```json
// pages.json - 页面配置
{
  "pages": [
    {
      "path": "pages/home/index",
      "style": {
        "navigationBarTitleText": "首页",
        "enablePullDownRefresh": false
      }
    },
    {
      "path": "pages/user/profile",
      "style": {
        "navigationBarTitleText": "个人资料"
      }
    }
  ],
  "globalStyle": {
    "navigationBarTextStyle": "black",
    "navigationBarTitleText": "uni-app x",
    "navigationBarBackgroundColor": "#F8F8F8",
    "backgroundColor": "#F8F8F8",
    "app-plus": {
      "background": "#efeff4"
    }
  },
  "tabBar": {
    "color": "#7A7E83",
    "selectedColor": "#007aff",
    "borderStyle": "black",
    "backgroundColor": "#F8F8F8",
    "list": [
      {
        "pagePath": "pages/home/index",
        "iconPath": "static/home.png",
        "selectedIconPath": "static/home-active.png",
        "text": "首页"
      },
      {
        "pagePath": "pages/user/profile",
        "iconPath": "static/user.png",
        "selectedIconPath": "static/user-active.png",
        "text": "我的"
      }
    ]
  },
  "condition": {
    "current": 0,
    "list": [
      {
        "name": "",
        "path": "",
        "query": ""
      }
    ]
  }
}
```

---

## 🔧 开发环境搭建

### 1. 依赖管理
```json
// package.json
{
  "name": "uni-app-x-project",
  "version": "1.0.0",
  "description": "uni-app x 项目",
  "main": "main.uts",
  "scripts": {
    "build:app": "uni build --platform app",
    "build:web": "uni build --platform web",
    "dev:app": "uni dev --platform app",
    "dev:web": "uni dev --platform web",
    "type-check": "vue-tsc --noEmit",
    "lint": "eslint . --ext .vue,.ts,.uts",
    "lint:fix": "eslint . --ext .vue,.ts,.uts --fix"
  },
  "dependencies": {
    "@dcloudio/types": "^3.4.8",
    "vue": "^3.4.21"
  },
  "devDependencies": {
    "@dcloudio/uni-automator": "^3.0.0-4020420240930001",
    "@dcloudio/uni-cli-shared": "^3.0.0-4020420240930001",
    "@dcloudio/uni-stacktracey": "^3.0.0-4020420240930001",
    "@dcloudio/vite-plugin-uni": "^3.0.0-4020420240930001",
    "typescript": "^5.4.5",
    "vite": "^5.1.6",
    "vue-tsc": "^2.0.6"
  },
  "uni-app": {
    "scripts": {}
  }
}
```

### 2. 开发工具配置
```json
// .vscode/settings.json
{
  "typescript.preferences.quoteStyle": "single",
  "typescript.updateImportsOnFileMove.enabled": "always",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "files.associations": {
    "*.uvue": "vue"
  },
  "vetur.experimental.templateInterpolationService": true
}
```

```json
// .vscode/extensions.json
{
  "recommendations": [
    "uni-helper.uni-app-schemas",
    "uni-helper.uni-cloud-schemas", 
    "uni-helper.uni-ui-snippets",
    "Vue.volar"
  ]
}
```

---

## 📝 代码规范与约定

### 1. 命名约定
```uts
// 文件命名：kebab-case
// user-profile.uvue
// api-service.uts
// storage-utils.uts

// 组件命名：PascalCase
export default {
  name: 'UserProfile'
}

// 函数命名：camelCase
function getUserInfo(): UserInfo {
  return userInfo
}

// 常量命名：SCREAMING_SNAKE_CASE
const API_BASE_URL = 'https://api.example.com'
const MAX_RETRY_COUNT = 3

// 类型命名：PascalCase
type UserInfo = {
  id: number,
  name: string
}

interface ApiResponse<T> {
  code: number,
  data: T
}

// 变量命名：camelCase
const userCount = ref(0)
const isLoading = ref(false)
```

### 2. 注释规范
```uts
/**
 * 用户信息类型定义
 * @description 包含用户基本信息和状态
 */
export type UserInfo = {
  /** 用户ID */
  id: number,
  /** 用户名 */
  name: string,
  /** 邮箱地址 */
  email: string,
  /** 是否激活 */
  isActive: boolean
}

/**
 * 获取用户信息
 * @param userId 用户ID
 * @returns 返回用户信息Promise
 * @throws {Error} 当用户不存在时抛出错误
 * @example
 * ```ts
 * const user = await getUserInfo(123)
 * console.log(user.name)
 * ```
 */
export async function getUserInfo(userId: number): Promise<UserInfo> {
  // 参数验证
  if (userId <= 0) {
    throw new Error('用户ID必须大于0')
  }
  
  try {
    // 发送API请求
    const response = await http.get<UserInfo>(`/users/${userId}`)
    return response.data
  } catch (error) {
    // 错误处理
    console.error('获取用户信息失败:', error)
    throw error
  }
}
```

### 3. 错误处理规范
```uts
// utils/error-handler.uts

export class AppError extends Error {
  public code: string
  public statusCode: number
  public details?: any
  
  constructor(message: string, code: string = 'UNKNOWN_ERROR', statusCode: number = 500, details?: any) {
    super(message)
    this.name = 'AppError'
    this.code = code
    this.statusCode = statusCode
    this.details = details
  }
}

export class ValidationError extends AppError {
  constructor(message: string, details?: any) {
    super(message, 'VALIDATION_ERROR', 400, details)
    this.name = 'ValidationError'
  }
}

export class NetworkError extends AppError {
  constructor(message: string, details?: any) {
    super(message, 'NETWORK_ERROR', 500, details)
    this.name = 'NetworkError'
  }
}

/**
 * 全局错误处理器
 * @param error 错误对象
 * @param context 错误上下文
 */
export function handleError(error: Error, context: string = ''): void {
  // 错误日志记录
  console.error(`[${context}] Error:`, error)
  
  // 根据错误类型处理
  if (error instanceof ValidationError) {
    uni.showToast({
      title: error.message,
      icon: 'none',
      duration: 3000
    })
  } else if (error instanceof NetworkError) {
    uni.showToast({
      title: '网络请求失败，请检查网络连接',
      icon: 'none',
      duration: 3000
    })
  } else {
    uni.showToast({
      title: '操作失败，请重试',
      icon: 'none',
      duration: 3000
    })
  }
  
  // 错误上报（生产环境）
  if (process.env.NODE_ENV === 'production') {
    reportError(error, context)
  }
}

/**
 * 错误上报
 * @param error 错误对象
 * @param context 错误上下文
 */
function reportError(error: Error, context: string): void {
  // 实现错误上报逻辑
  // 可以上报到第三方服务如 Sentry、Bugsnag 等
}
```

---

## 🧪 测试策略

### 1. 单元测试
```uts
// tests/utils/format.test.uts
import { describe, it, expect } from '@jest/globals'
import { formatDate, formatCurrency, validateEmail } from '../../src/utils/format.uts'

describe('Format Utils', () => {
  describe('formatDate', () => {
    it('should format date correctly', () => {
      const date = new Date('2024-03-15')
      const formatted = formatDate(date, 'YYYY-MM-DD')
      expect(formatted).toBe('2024-03-15')
    })
    
    it('should use default format when not specified', () => {
      const date = new Date('2024-03-15')
      const formatted = formatDate(date)
      expect(formatted).toBe('2024-03-15')
    })
  })
  
  describe('formatCurrency', () => {
    it('should format currency with default symbol', () => {
      const formatted = formatCurrency(123.45)
      expect(formatted).toBe('¥123.45')
    })
    
    it('should format currency with custom symbol', () => {
      const formatted = formatCurrency(123.45, '$')
      expect(formatted).toBe('$123.45')
    })
  })
  
  describe('validateEmail', () => {
    it('should validate correct email', () => {
      expect(validateEmail('test@example.com')).toBe(true)
    })
    
    it('should invalidate incorrect email', () => {
      expect(validateEmail('invalid-email')).toBe(false)
    })
  })
})
```

### 2. 组件测试
```uts
// tests/components/BaseButton.test.uts
import { mount } from '@vue/test-utils'
import BaseButton from '../../src/components/base/BaseButton.uvue'

describe('BaseButton Component', () => {
  it('should render with correct text', () => {
    const wrapper = mount(BaseButton, {
      props: {
        text: 'Click Me'
      }
    })
    
    expect(wrapper.text()).toContain('Click Me')
  })
  
  it('should emit click event', async () => {
    const wrapper = mount(BaseButton, {
      props: {
        text: 'Click Me'
      }
    })
    
    await wrapper.trigger('click')
    expect(wrapper.emitted('click')).toHaveLength(1)
  })
  
  it('should be disabled when disabled prop is true', () => {
    const wrapper = mount(BaseButton, {
      props: {
        text: 'Click Me',
        disabled: true
      }
    })
    
    expect(wrapper.classes()).toContain('button--disabled')
  })
})
```

### 3. E2E测试
```javascript
// tests/e2e/user-flow.test.js
const { device, element, by, expect } = require('detox')

describe('User Flow', () => {
  beforeAll(async () => {
    await device.launchApp()
  })

  beforeEach(async () => {
    await device.reloadReactNative()
  })

  it('should complete user login flow', async () => {
    // 导航到登录页面
    await element(by.text('登录')).tap()
    
    // 输入用户名和密码
    await element(by.id('username-input')).typeText('testuser')
    await element(by.id('password-input')).typeText('password123')
    
    // 点击登录按钮
    await element(by.id('login-button')).tap()
    
    // 验证登录成功
    await expect(element(by.text('欢迎回来'))).toBeVisible()
  })
  
  it('should handle login error', async () => {
    await element(by.text('登录')).tap()
    
    // 输入错误的凭据
    await element(by.id('username-input')).typeText('wronguser')
    await element(by.id('password-input')).typeText('wrongpass')
    
    await element(by.id('login-button')).tap()
    
    // 验证错误提示
    await expect(element(by.text('用户名或密码错误'))).toBeVisible()
  })
})
```

---

## 🚀 构建与部署

### 1. 构建配置
```javascript
// vite.config.ts
import { defineConfig } from 'vite'
import uni from '@dcloudio/vite-plugin-uni'

export default defineConfig({
  plugins: [uni()],
  server: {
    port: 3000,
    host: '0.0.0.0'
  },
  build: {
    target: 'es2015',
    minify: 'terser',
    terserOptions: {
      compress: {
        drop_console: true,
        drop_debugger: true
      }
    }
  },
  define: {
    __APP_VERSION__: JSON.stringify(process.env.npm_package_version)
  }
})
```

### 2. CI/CD流水线
```yaml
# .github/workflows/ci.yml
name: CI/CD

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run linter
        run: npm run lint
      
      - name: Run type check
        run: npm run type-check
      
      - name: Run tests
        run: npm run test
  
  build:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build for production
        run: npm run build:app
        
      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build-files
          path: dist/
```

### 3. 版本管理
```json
// scripts/version.js
const fs = require('fs')
const path = require('path')

const packagePath = path.join(__dirname, '../package.json')
const manifestPath = path.join(__dirname, '../manifest.json')

const package = require(packagePath)
const manifest = require(manifestPath)

// 更新版本号
const newVersion = process.argv[2]
if (newVersion) {
  package.version = newVersion
  manifest.versionName = newVersion
  manifest.versionCode = newVersion.replace(/\./g, '')
  
  fs.writeFileSync(packagePath, JSON.stringify(package, null, 2))
  fs.writeFileSync(manifestPath, JSON.stringify(manifest, null, 2))
  
  console.log(`Version updated to ${newVersion}`)
}
```

---

## 📊 监控与分析

### 1. 性能监控
```uts
// utils/performance.uts

export class PerformanceMonitor {
  private static marks: Map<string, number> = new Map()
  private static metrics: Map<string, number[]> = new Map()
  
  static mark(name: string): void {
    this.marks.set(name, Date.now())
  }
  
  static measure(name: string, startMark: string): number {
    const startTime = this.marks.get(startMark)
    if (!startTime) {
      console.warn(`Start mark '${startMark}' not found`)
      return 0
    }
    
    const duration = Date.now() - startTime
    
    // 记录指标
    if (!this.metrics.has(name)) {
      this.metrics.set(name, [])
    }
    this.metrics.get(name)!.push(duration)
    
    console.log(`Performance [${name}]: ${duration}ms`)
    return duration
  }
  
  static getMetrics(): Map<string, {
    average: number,
    min: number,
    max: number,
    count: number
  }> {
    const result = new Map()
    
    this.metrics.forEach((values, name) => {
      const average = values.reduce((a, b) => a + b, 0) / values.length
      const min = Math.min(...values)
      const max = Math.max(...values)
      
      result.set(name, {
        average: Math.round(average),
        min,
        max,
        count: values.length
      })
    })
    
    return result
  }
  
  static report(): void {
    const metrics = this.getMetrics()
    console.log('Performance Report:', metrics)
    
    // 发送到分析服务
    this.sendToAnalytics(metrics)
  }
  
  private static sendToAnalytics(metrics: any): void {
    // #ifdef APP
    // 发送到统计服务
    // #endif
  }
}

// 使用示例
export function withPerformanceTracking<T extends any[], R>(
  fn: (...args: T) => R,
  name: string
): (...args: T) => R {
  return (...args: T): R => {
    PerformanceMonitor.mark(`${name}_start`)
    const result = fn(...args)
    PerformanceMonitor.measure(name, `${name}_start`)
    return result
  }
}
```

### 2. 错误监控
```uts
// utils/error-tracking.uts

export interface ErrorReport {
  message: string
  stack?: string
  userAgent: string
  timestamp: number
  userId?: string
  pagePath: string
  context?: any
}

export class ErrorTracker {
  private static errorQueue: ErrorReport[] = []
  private static maxQueueSize = 100
  
  static init(): void {
    // 全局错误捕获
    // #ifdef APP
    process.on('uncaughtException', (error) => {
      this.captureError(error, 'uncaughtException')
    })
    
    process.on('unhandledRejection', (reason) => {
      this.captureError(new Error(String(reason)), 'unhandledRejection')
    })
    // #endif
    
    // 定期上报错误
    setInterval(() => {
      this.flushErrors()
    }, 30000) // 30秒上报一次
  }
  
  static captureError(error: Error, context?: string): void {
    const report: ErrorReport = {
      message: error.message,
      stack: error.stack,
      userAgent: uni.getSystemInfoSync().platform,
      timestamp: Date.now(),
      pagePath: getCurrentPages()[getCurrentPages().length - 1]?.route || '',
      context
    }
    
    this.errorQueue.push(report)
    
    if (this.errorQueue.length > this.maxQueueSize) {
      this.errorQueue.shift() // 移除最老的错误
    }
    
    console.error('Error captured:', report)
  }
  
  static async flushErrors(): Promise<void> {
    if (this.errorQueue.length === 0) return
    
    const errors = [...this.errorQueue]
    this.errorQueue = []
    
    try {
      // 发送到错误收集服务
      await this.sendErrors(errors)
    } catch (e) {
      // 发送失败，重新加入队列
      this.errorQueue.unshift(...errors)
    }
  }
  
  private static async sendErrors(errors: ErrorReport[]): Promise<void> {
    // 实现错误上报逻辑
    console.log('Sending errors:', errors)
  }
}
```

---

## 📋 开发流程检查清单

### 项目初始化
- [ ] 创建规范的项目结构
- [ ] 配置开发环境和工具
- [ ] 设置代码规范和格式化
- [ ] 初始化版本控制

### 开发阶段
- [ ] 遵循命名约定和代码规范
- [ ] 编写完整的类型定义
- [ ] 实现错误处理机制
- [ ] 添加必要的注释和文档

### 质量保证
- [ ] 编写单元测试和组件测试
- [ ] 进行代码审查
- [ ] 性能测试和优化
- [ ] 跨平台兼容性测试

### 部署发布
- [ ] 配置CI/CD流水线
- [ ] 版本管理和发布日志
- [ ] 监控和错误跟踪
- [ ] 用户反馈收集

### 维护迭代
- [ ] 定期依赖更新
- [ ] 性能监控分析
- [ ] 用户体验优化
- [ ] 技术债务清理

此指南提供了uni-app x项目开发的完整框架，有助于团队建立规范的开发流程和高质量的代码标准。

````
