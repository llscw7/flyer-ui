---
mode: agent
---
# UNI-APP X 开发总览与快速入门

这是uni-app x项目开发的总体指引文件，汇总了所有开发规范和最佳实践，为开发者提供快速上手的完整指南。

## 📚 提示文件体系

本项目包含以下开发提示文件，建议按顺序学习：

### 1. 基础语法篇
- **`UTS_GUIDE.prompt.md`** - UTS语言基础规范和Vue3组合式API使用指南
- **`UTS_FUNC.prompt.md`** - UTS函数定义、调用、作用域和闭包完整指南
- **`UTS_VS_TS.prompt.md`** - UTS与TypeScript核心差异对比
- **`UVUE_COMPONENT.prompt.md`** - UVUE组件开发完整指南

### 2. 架构设计篇  
- **`UTS_MODULE.prompt.md`** - UTS状态管理与模块化开发指南
- **`REACTIVE_SYSTEM.prompt.md`** - 响应式系统开发指南
- **`PROJECT_ARCHITECTURE.prompt.md`** - 项目架构与开发流程指南

### 3. 跨端适配篇
- **`CROSS_PLATFORM.prompt.md`** - 跨端开发最佳实践
- **`EDIT_RULE.prompt.md`** - 代码编辑和重构规则

## 🚀 快速开始

### 第一步：环境准备
1. 安装HBuilderX开发工具
2. 创建uni-app x项目
3. 配置TypeScript支持
4. 安装必要的开发插件

### 第二步：项目结构
```
your-project/
├── src/
│   ├── components/     # 可复用组件
│   ├── pages/         # 页面文件
│   ├── store/         # 状态管理
│   ├── utils/         # 工具函数
│   ├── services/      # API服务
│   └── types/         # 类型定义
├── static/            # 静态资源
├── App.uvue          # 应用主组件
├── main.uts          # 应用入口
├── manifest.json     # 应用配置
└── pages.json        # 页面配置
```

### 第三步：核心概念掌握
1. **UTS语言特性** - 强类型、严格空值检查、条件编译
2. **组件开发** - 选项式API vs 组合式API、Props、Events
3. **状态管理** - ref、reactive、computed、watch
4. **跨端适配** - 条件编译、平台API适配

## 💡 核心开发原则

### 1. 类型安全优先
```uts
// ✅ 推荐：明确类型定义
const userInfo = ref<UserInfo | null>(null)
const loading = ref<boolean>(false)

// ❌ 避免：使用any类型
const data: any = {}
```

### 2. 响应式最佳实践
```uts
// ✅ 基础类型用ref
const count = ref(0)
const message = ref('')

// ✅ 对象类型用reactive  
const userState = reactive({
  name: '',
  age: 0
})
```

### 3. 条件编译处理差异
```uts
// 平台差异处理
// #ifdef APP-ANDROID
const platform = 'android'
// #endif

// #ifdef APP-IOS
const platform = 'ios'
// #endif
```

### 4. 组合式API组织代码
```uts
<script setup lang="uts">
// 1. 导入
import { ref, computed, watch, onMounted } from 'vue'

// 2. Props和Emits定义
const props = defineProps<{
  title: string
}>()

const emit = defineEmits<{
  change: [value: string]
}>()

// 3. 响应式数据
const loading = ref(false)
const data = reactive({ items: [] })

// 4. 计算属性
const isEmpty = computed(() => data.items.length === 0)

// 5. 方法定义
function handleChange(): void {
  emit('change', 'new value')
}

// 6. 生命周期
onMounted(() => {
  loadData()
})
</script>
```

## 🔧 开发工具配置

### VSCode配置
```json
// .vscode/settings.json
{
  "files.associations": {
    "*.uvue": "vue"
  },
  "typescript.preferences.quoteStyle": "single",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```

### ESLint配置
```javascript
// .eslintrc.js
module.exports = {
  extends: [
    '@dcloudio/eslint-config-uni'
  ],
  rules: {
    'no-console': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
    'no-debugger': process.env.NODE_ENV === 'production' ? 'warn' : 'off'
  }
}
```

## 📖 学习路径建议

### 新手入门（1-2周）
1. 阅读`UTS_GUIDE.prompt.md`掌握基础语法
2. 学习`UVUE_COMPONENT.prompt.md`了解组件开发
3. 练习创建简单的页面和组件

### 进阶开发（2-3周）
1. 深入学习`UTS_FUNC.prompt.md`和`UTS_VS_TS.prompt.md`
2. 掌握`REACTIVE_SYSTEM.prompt.md`中的响应式系统
3. 实践状态管理和数据流设计

### 高级应用（3-4周）  
1. 学习`UTS_MODULE.prompt.md`模块化开发
2. 掌握`CROSS_PLATFORM.prompt.md`跨端适配技术
3. 应用`PROJECT_ARCHITECTURE.prompt.md`搭建完整项目

### 生产级开发（持续）
1. 建立代码规范和团队协作流程
2. 实施性能优化和监控策略  
3. 持续迭代和技术栈升级

## 🐛 常见问题解决

### 类型错误
- **问题**：Property 'xxx' does not exist on type 'xxx'
- **解决**：检查类型定义，使用类型断言或类型保护

### 响应式数据问题
- **问题**：数据变化但视图不更新
- **解决**：检查是否正确使用ref/reactive，避免直接赋值根对象

### 跨端兼容性问题
- **问题**：某些API在特定平台不可用
- **解决**：使用条件编译，提供平台特定实现

### 性能问题
- **问题**：页面卡顿或内存泄漏
- **解决**：检查大数据列表、计算属性缓存、及时清理监听器

## 📋 质量检查清单

### 代码质量
- [ ] 所有变量都有明确类型注解
- [ ] 使用合适的响应式API（ref/reactive）
- [ ] 组件Props和Events有完整类型定义
- [ ] 错误处理完善，边界情况考虑充分

### 跨端兼容
- [ ] 使用条件编译处理平台差异
- [ ] API调用前检查平台支持性
- [ ] 样式适配不同屏幕尺寸
- [ ] 测试多平台功能一致性

### 性能优化  
- [ ] 合理使用计算属性缓存
- [ ] 避免不必要的深度监听
- [ ] 图片懒加载和尺寸优化
- [ ] 及时清理定时器和监听器

### 用户体验
- [ ] 提供合适的加载状态
- [ ] 错误信息友好且可操作
- [ ] 交互反馈及时清晰
- [ ] 适配各种设备和网络环境

## 🔗 相关资源

### 官方文档
- [uni-app x 官方文档](https://uniapp.dcloud.net.cn/uni-app-x/)
- [UTS 语法说明](https://uniapp.dcloud.net.cn/tutorial/syntax-uts.html)
- [Vue 3 官方文档](https://cn.vuejs.org/)

### 开发工具
- [HBuilderX](https://www.dcloud.io/hbuilderx.html)
- [uni-app开发者中心](https://dev.dcloud.net.cn/)

### 社区支持
- [DCloud 社区](https://ask.dcloud.net.cn/)
- [GitHub Issues](https://github.com/dcloudio/uni-app/issues)

---

## 总结

本提示文件系统为uni-app x开发提供了完整的指导框架，涵盖了从基础语法到项目架构的各个方面。建议开发者根据自身经验选择合适的学习路径，循序渐进地掌握uni-app x的开发技能。

记住关键原则：
- **类型安全优先**
- **响应式思维**  
- **跨端一致性**
- **性能与体验并重**

通过系统学习这些提示文件，相信你能够构建出高质量、可维护的uni-app x应用程序。

````
