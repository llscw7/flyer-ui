# 📚 Flyer UI 开发文档索引

欢迎来到 Flyer UI 组件库！这里是所有开发相关文档的导航。

## 🚀 快速开始

- **[项目主文档](../README.md)** - 项目概述、特性介绍、安装使用
- **[快速参考卡片](QUICK_REFERENCE.md)** - UTS 语法速查，常见错误对比

## 📖 开发规范

- **[Copilot 指令](copilot-instructions.md)** - GitHub Copilot 的核心指令和规范
- **[UTS 开发规范](UTS_GUIDE.md)** - UTS 语言详细开发规范和最佳实践
- **[项目配置指南](PROJECT_CONFIG.md)** - 环境配置、easycom设置、调试指南
- **[字体图标指南](ICON_GUIDE.md)** - uni-icon和iconfont字体图标使用指南

## 🤖 Copilot 协作

- **[Copilot 工作流程](COPILOT_WORKFLOW.md)** - 如何高效使用 GitHub Copilot 开发组件

## 📂 项目结构

```
flyer-ui/
├── .github/                     # GitHub 和开发相关文档
│   ├── copilot-instructions.md  # Copilot 核心指令
│   ├── UTS_GUIDE.md             # UTS 开发规范  
│   ├── PROJECT_CONFIG.md        # 项目配置指南
│   ├── QUICK_REFERENCE.md       # 快速参考卡片
│   ├── COPILOT_WORKFLOW.md      # Copilot 工作流程
│   ├── ICON_GUIDE.md            # 字体图标使用指南
│   └── DOCS_INDEX.md            # 本文档索引
├── components/                  # 组件源码目录
│   ├── flyer-button/           # Button 组件
│   └── flyer-icon/             # Icon 组件
├── pages/                      # 示例页面目录
│   ├── index/                  # 首页（组件展示列表）
│   └── examples/               # 组件示例页面
├── common/                     # 通用资源
│   └── flyer-ui.scss          # 样式变量文件
└── README.md                   # 项目主文档
```

## 🎯 开发流程

### 1. 新组件开发
1. 阅读 [UTS_GUIDE.md](UTS_GUIDE.md) 了解语法规范
2. 参考 [COPILOT_WORKFLOW.md](COPILOT_WORKFLOW.md) 使用 Copilot
3. 使用 [QUICK_REFERENCE.md](QUICK_REFERENCE.md) 查验语法
4. 按照 [PROJECT_CONFIG.md](PROJECT_CONFIG.md) 配置和测试

### 2. 问题排查
1. 检查 [copilot-instructions.md](copilot-instructions.md) 核心规范
2. 对照 [QUICK_REFERENCE.md](QUICK_REFERENCE.md) 排查语法错误
3. 参考 [UTS_GUIDE.md](UTS_GUIDE.md) 的错误处理章节

### 3. 性能优化
1. 遵循 [PROJECT_CONFIG.md](PROJECT_CONFIG.md) 的性能建议
2. 应用 [UTS_GUIDE.md](UTS_GUIDE.md) 的优化规范

## 🔗 相关链接

- [uni-app x 官方文档](https://doc.dcloud.net.cn/uni-app-x/)
- [UTS 语法说明](https://doc.dcloud.net.cn/uni-app-x/uts/)
- [uvue 组件规范](https://doc.dcloud.net.cn/uni-app-x/component/)
- [ucss 样式规范](https://doc.dcloud.net.cn/uni-app-x/css/)

---

💡 **提示**: 在使用 GitHub Copilot 时，可以直接引用这些文档的内容来获得更准确的代码建议！
