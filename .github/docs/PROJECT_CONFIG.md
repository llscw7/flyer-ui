# Flyer UI 项目配置说明

## 环境要求

- **HBuilderX**: 3.9.0+
- **uni-app x**: 最新版本
- **Vue**: 3.x (使用组合式 API)
- **UTS**: 跨平台强类型语言

## Vue 3 组合式 API 配置

本项目已全面采用 Vue 3 组合式 API，所有组件使用 `<script setup lang="uts">` 语法开发：

### 核心 API 配置

```uts
// 从 Vue 导入响应式 API
import { 
  ref, 
  reactive, 
  computed, 
  watch, 
  watchEffect,
  onMounted, 
  onUnmounted,
  onUpdated 
} from 'vue'

// 组件选项定义
defineOptions({
  name: 'FlyerComponent',
  inheritAttrs: false
})

// Props 类型定义
const props = defineProps<{
  type?: string,
  disabled?: boolean,
  size?: 'large' | 'normal' | 'small' | 'mini'
}>()

// 事件定义
const emit = defineEmits<{
  click: [event: any],
  change: [value: string | number]
}>()
```

## easycom 配置

在 `pages.json` 中配置组件自动注册：

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

这样配置后，所有以 `flyer-` 开头的组件都会自动注册，无需手动import。

## 样式变量系统

### 主色彩系统

```scss
// 品牌色彩
$flyer-color-primary: #007aff;    // 主题色
$flyer-color-success: #4cd964;   // 成功色
$flyer-color-warning: #f0ad4e;   // 警告色
$flyer-color-danger: #dd524d;    // 危险色
$flyer-color-info: #909399;      // 信息色
```

### 尺寸系统

```scss
// 按钮高度
$flyer-button-large-height: 50px;
$flyer-button-normal-height: 44px;
$flyer-button-small-height: 36px;
$flyer-button-mini-height: 28px;

// 圆角
$flyer-border-radius-base: 4px;
$flyer-border-radius-round: 20px;

// 间距
$flyer-spacing-base: 12px;
$flyer-spacing-large: 16px;
```

## 组件开发最佳实践

### 1. 组件文件结构

```
components/
└── flyer-[component]/
    └── flyer-[component].uvue
```

### 2. 组件基础模板（组合式 API）

```vue
<template>
  <view class="flyer-[component]" :class="computedClass" :style="customStyle">
    <!-- 组件内容 -->
  </view>
</template>

<script setup lang="uts">
// 导入 Vue APIs
import { ref, reactive, computed, watch, onMounted } from 'vue'

// 定义组件选项
defineOptions({
  name: 'Flyer[Component]'
})

// 定义 props
const props = defineProps<{
  type?: string,
  disabled?: boolean,
  customStyle?: UTSJSONObject
}>()

// 定义事件
const emit = defineEmits<{
  click: [event: any]
}>()

// 响应式数据
const isLoading = ref(false)

// 计算属性
const computedClass = computed(() => {
  return [
    `flyer-[component]--${props.type || 'default'}`,
    {
      'flyer-[component]--disabled': props.disabled
    }
  ]
})

// 方法定义
function handleClick(event: any) {
  if (props.disabled) {
    return
  }
  emit('click', event)
}

// 生命周期
onMounted(() => {
  console.log('组件已挂载')
})
</script>

<style>
.flyer-[component] {
  /* 组件样式 */
}
</style>
```
  },
  data() {
    return {
      // 内部状态
    }
  },
  computed: {
    computedClass(): string {
      // 计算样式类
      return ''
    }
  },
  methods: {
    // 组件方法
  }
}
</script>

<style>
.flyer-[component] {
  /* 组件样式 */
}
</style>
```

### 3. Props 设计原则

- 使用具体的基础类型（String、Number、Boolean、Object）
- 提供合理的默认值
- 添加清晰的注释说明可选值
- 避免复杂的嵌套对象

### 4. 事件设计原则

- 事件名使用小写加连字符
- 传递有意义的参数
- 提供事件说明文档

## 平台兼容性

### 条件编译使用

```uts
// APP通用
// #ifdef APP
// APP相关代码
// #endif

// Android特定
// #ifdef APP-ANDROID
// Android特定代码
// #endif

// iOS特定
// #ifdef APP-IOS
// iOS特定代码
// #endif
```

### 样式兼容性

- 只使用flex布局
- 避免使用复杂CSS选择器
- 文字样式只能应用于text组件
- 使用安全的CSS属性

## 调试指南

### 1. 常见错误排查

**类型错误**
- 检查变量类型声明
- 确认条件语句使用布尔值
- 验证函数参数类型

**样式问题**
- 确认只使用了类选择器
- 检查flex布局设置
- 验证文字样式应用位置

**组件注册问题**
- 检查easycom配置
- 确认组件文件路径
- 验证组件命名规范

### 2. 性能优化建议

- 避免在模板中进行复杂计算
- 合理使用computed属性
- 减少不必要的响应式数据
- 及时清理事件监听

### 3. 真机测试

- 在Android设备上测试触摸交互
- 在iOS设备上验证样式一致性
- 测试不同屏幕尺寸的适配
- 验证性能表现

## 发布规范

### 版本号规范

遵循语义化版本 (Semantic Versioning)：
- 主版本号：不兼容的API修改
- 次版本号：向下兼容的功能性新增
- 修订号：向下兼容的问题修正

### 提交信息规范

```
feat: 新增功能
fix: 修复bug
docs: 文档更新
style: 代码格式调整
refactor: 重构代码
test: 测试相关
chore: 构建过程或辅助工具变动
```

### 发布检查清单

- [ ] 所有组件功能正常
- [ ] 示例页面完整
- [ ] 文档说明清晰
- [ ] 真机测试通过
- [ ] 代码符合规范
- [ ] 版本号已更新

---

## 常用命令

```bash
# 查看项目文件
ls -la

# 检查git状态
git status

# 提交代码
git add .
git commit -m "feat: 新增Button组件"
git push
```

## 技术支持

- [uni-app x官方文档](https://doc.dcloud.net.cn/uni-app-x/)
- [UTS语法说明](https://doc.dcloud.net.cn/uni-app-x/uts/)
- [uvue组件规范](https://doc.dcloud.net.cn/uni-app-x/component/)
- [ucss样式规范](https://doc.dcloud.net.cn/uni-app-x/css/)
