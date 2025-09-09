# Flyer UI

基于 uni-app x 的移动端组件库

## 🎯 项目重构说明（v1.1.0）

本次重构严格按照 **CROSS_PLATFORM.prompt.md** 定义的规则进行，主要改进包括：

### 📱 跨平台适配优化
- ✅ **App.uvue**: 重构为现代 Composition API，增强平台检测和主题适配
- ✅ **条件编译**: 规范化 `#ifdef`/`#endif` 使用，支持 Android/iOS/Web/HarmonyOS
- ✅ **安全区域**: 统一处理刘海屏和异形屏适配
- ✅ **主题系统**: 支持亮色/暗色主题自动切换

### 🔧 组件代码规范化  
- ✅ **flyer-button**: 优化 TypeScript 类型定义，使用计算属性提升性能
- ✅ **flyer-icon**: 简化图标映射逻辑，增强字体图标支持
- ✅ **统一命名**: 严格按照 UTS/UVUE 规范重构所有组件

### 🛠️ 工具类统一封装
- ✅ **PlatformUtils**: 跨平台系统信息获取、设备检测、主题管理
- ✅ **NavigationManager**: 统一导航封装，错误处理、防抖、智能返回
- ✅ **模块化设计**: 工具类按功能模块化，便于维护和扩展

### 📋 代码质量提升
- ✅ **删除冗余代码**: 清理第三方组件和无用逻辑
- ✅ **性能优化**: 使用计算属性、减少重复计算、优化渲染性能
- ✅ **错误处理**: 统一异常捕获和用户友好提示
- ✅ **TypeScript**: 严格类型检查，提升代码可靠性

### 🎨 用户体验改进
- ✅ **响应式设计**: 支持平板设备和横竖屏切换
- ✅ **加载状态**: 优化页面跳转和组件交互反馈
- ✅ **视觉设计**: 现代化 UI 设计，支持深色主题
- ✅ **无障碍**: 增强组件可访问性支持

### 📚 开发规范完善
- ✅ **跨平台指南**: 详细的条件编译和API适配规范
- ✅ **组件开发**: UVUE组件和UTS模块开发最佳实践  
- ✅ **架构设计**: 项目结构和模块划分规范
- ✅ **文档体系**: 完整的开发文档和快速参考

---

## 特性

- 🎯 专为 uni-app x 设计，完美兼容
- 📱 移动端优先，支持 APP-Android、APP-iOS 
- 🎨 现代化设计语言，简洁美观
- 💪 使用 UTS 语言开发，类型安全
- 🚀 性能优异，原生体验
- 🔧 灵活可定制，支持主题配置

## 快速开始

### 安装使用

1. 将 `components` 文件夹复制到你的项目根目录
2. 在 `pages.json` 中配置 easycom 规则：

```json
{
  "easycom": {
    "autoscan": true,
    "custom": {
      "^flyer-(.*)": "@/components/flyer-$1/flyer-$1.uvue"
    }
  }
}
```

### 基础用法

```vue
<template>
  <flyer-button type="primary" text="主要按钮" @click="handleClick"></flyer-button>
</template>

<script>
export default {
  methods: {
    handleClick() {
      console.log('按钮被点击了')
    }
  }
}
</script>
```

## 组件列表

### 基础组件

- [x] **Button 按钮** - 按钮用于触发一个操作
- [x] **Icon 图标** - 语义化的矢量图形，支持内置uni-icon和自定义iconfont
- [x] **Dialog 对话框** - 模态对话框，在浮层中显示，引导用户进行相关操作
- [x] **Popup 底部弹窗** - 从底部弹出的模态层，常用于展示操作菜单
- [ ] **Input 输入框** - 通过鼠标或键盘输入字符  
- [ ] **Card 卡片** - 将信息聚合在卡片容器中显示
- [ ] **Loading 加载** - 加载过程中的过渡状态

### 反馈组件

- [x] **Dialog 对话框** - 全局状态管理的模态对话框组件
- [x] **Popup 底部弹窗** - 全局状态管理的底部弹窗组件
- [ ] **Toast 轻提示** - 一种轻量级反馈组件
- [ ] **Modal 弹窗** - 模态对话框
- [ ] **ActionSheet 操作菜单** - 底部弹起的操作菜单

### 展示组件

- [ ] **Avatar 头像** - 用来代表用户或事物
- [ ] **Badge 徽标** - 出现在按钮、图标旁的数字或状态标记
- [ ] **Progress 进度条** - 用于展示操作进度

### 导航组件

- [ ] **Navbar 导航栏** - 页面顶部导航
- [ ] **Tabbar 标签栏** - 底部选项卡切换
- [ ] **Steps 步骤条** - 引导用户按照流程完成任务

### 布局组件

- [ ] **Layout 布局** - 协助进行页面级整体布局
- [ ] **Grid 宫格** - 在水平和垂直方向，将布局切分成若干等大的区块
- [ ] **Divider 分割线** - 区隔内容的分割线

## 开发指南

### 目录结构

```
flyer-ui/
├── App.uvue                    # 应用入口文件（已重构为 Composition API）
├── components/                 # 组件源码目录
│   ├── flyer-button/          # Button 按钮组件（已优化）
│   ├── flyer-icon/            # Icon 图标组件（已优化）
│   ├── flyer-dialog/          # Dialog 对话框组件
│   ├── flyer-popup/           # Popup 弹窗组件  
│   └── flyer-actionsheet/     # ActionSheet 组件
├── pages/                     # 页面目录
│   ├── index/                 # 首页（已重构，使用工具类）
│   └── examples/              # 组件示例页面
│       ├── button/            # Button 示例（已优化）
│       ├── icon/              # Icon 示例
│       ├── dialog/            # Dialog 示例
│       ├── popup/             # Popup 示例
│       └── actionsheet/       # ActionSheet 示例
├── utils/                     # 工具类目录（新增）
│   ├── platform.uts          # 跨平台工具类
│   ├── navigation.uts         # 导航工具类
│   └── index.uts              # 工具类统一导出
├── .github/prompts/           # 开发规范文档
│   ├── CROSS_PLATFORM.prompt.md    # 跨平台开发指南
│   ├── UVUE_COMPONENT.prompt.md    # UVUE 组件开发规范
│   ├── UTS_MODULE.prompt.md        # UTS 模块开发规范
│   ├── REACTIVE_SYSTEM.prompt.md   # 响应式系统指南
│   ├── PROJECT_ARCHITECTURE.prompt.md # 项目架构规范
│   └── README.prompt.md             # 文档编写规范
├── static/                    # 静态资源
├── package.json              # 项目配置
└── README.md                 # 项目说明
```

### 开发规范文档

详细的开发规范请查看 `.github` 目录下的文档：

- **[📚 文档索引](.github/DOCS_INDEX.md)** - 所有开发文档的导航
- **[🤖 Copilot 指令](.github/copilot-instructions.md)** - GitHub Copilot 核心规范
- **[📖 UTS 开发规范](.github/UTS_GUIDE.md)** - UTS 语言详细开发指南
- **[⚡ 快速参考](.github/QUICK_REFERENCE.md)** - UTS 语法速查卡片
- **[🔧 项目配置](.github/PROJECT_CONFIG.md)** - 环境配置和最佳实践
- **[🚀 Copilot 工作流程](.github/COPILOT_WORKFLOW.md)** - Copilot 协作开发指南
- **[🏪 Store 组件开发指南](.github/STORE_COMPONENT_GUIDE.md)** - 全局状态管理组件开发
- **[📋 Store 组件快速参考](.github/STORE_COMPONENT_QUICK_REF.md)** - Store 组件快速创建模板

### 组件开发规范

1. 组件命名采用 `flyer-` 前缀
2. 使用 UTS 语言编写，确保类型安全
3. 遵循 uni-app x 的组件规范
4. 支持 easycom 自动注册
5. 提供完整的示例和文档

### 样式规范

- 使用 flex 布局
- 遵循 ucss 样式规范  
- 支持主题定制
- 响应式适配

## 浏览器支持

- APP-Android 5.0+
- APP-iOS 9.0+

## 开源协议

[MIT License](https://opensource.org/licenses/MIT)

## 更新日志

### v1.1.0 (2024-01-XX) - 跨平台重构版本

**重大重构**
- 🔄 按照 CROSS_PLATFORM.prompt.md 规范全面重构项目
- 🎯 App.uvue 升级为 Composition API，增强平台适配
- 🛠️ 新增 utils 工具类系统，提供统一的平台和导航管理
- 📱 完善跨平台条件编译和安全区域适配
- 🎨 优化组件性能和用户体验

**组件优化**
- ✨ flyer-button: 重构类型系统，优化计算属性和事件处理
- ✨ flyer-icon: 简化图标映射，增强字体图标支持
- 🔧 统一组件命名规范和代码风格

**新增功能**
- 🆕 PlatformUtils: 跨平台系统信息获取和设备检测
- 🆕 NavigationManager: 统一导航封装，支持防抖和错误处理
- 🆕 主题系统: 支持亮色/暗色主题自动切换
- 🆕 响应式设计: 支持平板设备和不同屏幕尺寸

**代码质量**
- 🧹 清理第三方组件和冗余代码
- 🔒 增强 TypeScript 类型安全
- ⚡ 性能优化和内存管理改进
- 📋 完善错误处理和用户提示

### v1.0.0

- 🎉 初始版本发布
- ✨ 新增 Button 按钮组件
- 📝 完善组件文档和示例
