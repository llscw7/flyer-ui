# Flyer UI

基于 uni-app x 的移动端组件库

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
- [ ] **Input 输入框** - 通过鼠标或键盘输入字符  
- [ ] **Card 卡片** - 将信息聚合在卡片容器中显示
- [ ] **Loading 加载** - 加载过程中的过渡状态

### 反馈组件

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
├── components/              # 组件源码
│   └── flyer-button/       # Button组件
├── pages/                  # 示例页面
│   ├── index/              # 首页
│   └── examples/           # 组件示例
├── common/                 # 通用资源
│   └── flyer-ui.scss       # 样式变量
└── static/                 # 静态资源
```

### 开发规范文档

详细的开发规范请查看 `.github` 目录下的文档：

- **[📚 文档索引](.github/DOCS_INDEX.md)** - 所有开发文档的导航
- **[🤖 Copilot 指令](.github/copilot-instructions.md)** - GitHub Copilot 核心规范
- **[📖 UTS 开发规范](.github/UTS_GUIDE.md)** - UTS 语言详细开发指南
- **[⚡ 快速参考](.github/QUICK_REFERENCE.md)** - UTS 语法速查卡片
- **[🔧 项目配置](.github/PROJECT_CONFIG.md)** - 环境配置和最佳实践
- **[🚀 Copilot 工作流程](.github/COPILOT_WORKFLOW.md)** - Copilot 协作开发指南

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

### v1.0.0

- 🎉 初始版本发布
- ✨ 新增 Button 按钮组件
- 📝 完善组件文档和示例
