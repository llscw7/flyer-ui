# uni-app x 样式开发指南

## 概述

本文档提供 uni-app x 项目的样式开发指南，包括 CSS 变量使用、SCSS 支持、样式最佳实践等。

## CSS 变量 vs SCSS 变量

### ❌ 错误用法：使用未定义的 CSS 变量

```css
/* 错误：uni-app x 不支持未定义的 CSS 变量 */
.container {
    background-color: var(--bg-page); /* 未定义的 CSS 变量 */
    color: var(--text-primary); /* 未定义的 CSS 变量 */
}
```

### ✅ 正确用法：使用具体颜色值

```css
/* 正确：使用具体的颜色值 */
.container {
    background-color: #f2f3f5; /* 页面背景色 */
    color: #333333; /* 主要文本色 */
}
```

## 颜色规范

### 主题色彩
```css
/* 品牌色彩 */
--primary: #007aff;     /* 主色 */
--success: #4cd964;     /* 成功色 */
--warning: #f0ad4e;     /* 警告色 */
--danger: #dd524d;      /* 危险色 */
--info: #909399;        /* 信息色 */
```

### 中性色
```css
/* 文本色 */
--text-primary: #333333;     /* 主要文本 */
--text-regular: #606266;     /* 常规文本 */
--text-secondary: #909399;   /* 次要文本 */
--text-placeholder: #c0c4cc; /* 占位文本 */
--text-disabled: #c0c4cc;    /* 禁用文本 */
```

### 背景色
```css
/* 背景色 */
--bg-white: #ffffff;         /* 纯白背景 */
--bg-page: #f2f3f5;         /* 页面背景 */
--bg-light: #fafafa;        /* 浅色背景 */
```

### 边框色
```css
/* 边框色 */
--border-light: #ebeef5;        /* 浅色边框 */
--border-lighter: #f2f6fc;      /* 更浅边框 */
--border-extra-light: #fafafa;  /* 极浅边框 */
```

## SCSS 支持

### SCSS 变量定义
在 `common/flyer-ui.scss` 中定义项目变量：

```scss
// 品牌色彩
$flyer-color-primary: #007aff;
$flyer-color-success: #4cd964;

// 文本色
$flyer-color-text-primary: #333333;
$flyer-color-text-secondary: #909399;

// 背景色
$flyer-bg-color: #ffffff;
$flyer-bg-color-page: #f2f3f5;
```

### SCSS 变量使用

如果需要使用 SCSS 变量，确保正确导入：

```vue
<style lang="scss">
@import "../../common/flyer-ui.scss";

.container {
    background-color: $flyer-bg-color-page;
    color: $flyer-color-text-primary;
}
</style>
```

**注意：** uni-app x 对 SCSS 的支持可能有限，建议优先使用具体颜色值。

## 样式限制

### 不支持的特性

1. **CSS 变量**：uni-app x 不支持 `var(--variable)` 语法
2. **复杂伪类**：某些伪类选择器可能不支持
3. **媒体查询**：复杂的 `@media` 查询支持有限
4. **CSS 动画**：某些动画属性可能不支持

### ❌ 避免使用

```css
/* 不支持的伪类 */
.item:active { /* 可能不支持 */ }
.item:last-child { /* 可能不支持 */ }

/* 复杂媒体查询 */
@media (max-width: 768px) { /* 支持有限 */ }

/* CSS 变量 */
color: var(--text-color); /* 不支持 */

/* 复杂动画 */
transition: all 0.3s ease; /* 可能需要拆分 */
```

### ✅ 推荐使用

```css
/* 基础样式 */
.item {
    background-color: #ffffff;
    color: #333333;
    border: 1px solid #ebeef5;
}

/* 分离的动画属性 */
.item {
    transition-property: background-color;
    transition-duration: 0.3s;
    transition-timing-function: ease;
}
```

## 最佳实践

### 1. 组件样式结构

```vue
<template>
    <view class="flyer-component">
        <view class="flyer-component__header">
            <text class="flyer-component__title">标题</text>
        </view>
        <view class="flyer-component__body">
            内容
        </view>
    </view>
</template>

<style>
/* 组件根样式 */
.flyer-component {
    background-color: #ffffff;
    border-radius: 8px;
}

/* 组件元素样式 */
.flyer-component__header {
    padding: 16px;
    border-bottom: 1px solid #ebeef5;
}

.flyer-component__title {
    font-size: 16px;
    font-weight: 700;
    color: #333333;
}

.flyer-component__body {
    padding: 16px;
}
</style>
```

### 2. 状态类命名

```css
/* 状态类 */
.flyer-component--disabled {
    opacity: 0.6;
    pointer-events: none;
}

.flyer-component--loading {
    position: relative;
}

.flyer-component--active {
    background-color: #f0f9ff;
}
```

### 3. 平台适配

```css
/* 使用条件编译 */
.component {
    padding: 16px;
}

/* #ifdef APP */
.component {
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}
/* #endif */

/* #ifdef H5 */
.component {
    border: 1px solid #ebeef5;
}
/* #endif */
```

## 颜色值参考

### 常用颜色对照表

| 名称 | 十六进制 | 用途 |
|------|----------|------|
| 主色 | #007aff | 品牌主色、按钮、链接 |
| 成功色 | #4cd964 | 成功状态、确认操作 |
| 警告色 | #f0ad4e | 警告信息、待处理状态 |
| 危险色 | #dd524d | 错误信息、删除操作 |
| 信息色 | #909399 | 一般信息、提示文本 |
| 主文本 | #333333 | 标题、重要文本 |
| 次文本 | #909399 | 描述、说明文本 |
| 占位文本 | #c0c4cc | 输入框占位符 |
| 页面背景 | #f2f3f5 | 应用背景色 |
| 卡片背景 | #ffffff | 卡片、面板背景 |
| 边框色 | #ebeef5 | 分割线、边框 |

## 调试技巧

### 1. 样式不生效排查

1. 检查是否使用了不支持的 CSS 特性
2. 确认类名拼写正确
3. 验证颜色值格式正确
4. 查看开发者工具中的样式应用情况

### 2. 性能优化

```css
/* 避免深层嵌套 */
.component .header .title { /* 不推荐 */ }

/* 使用扁平化类名 */
.component__title { /* 推荐 */ }

/* 减少重复代码 */
.btn-base {
    padding: 8px 16px;
    border-radius: 4px;
    font-size: 14px;
}

.btn-primary {
    background-color: #007aff;
    color: #ffffff;
}
```

## 总结

1. **避免使用 CSS 变量**：使用具体颜色值替代
2. **简化样式选择器**：避免复杂的伪类和嵌套
3. **统一颜色规范**：使用标准化的颜色值
4. **遵循 BEM 命名**：保持样式结构清晰
5. **利用条件编译**：针对不同平台优化样式
6. **测试兼容性**：在目标平台上验证样式效果

通过遵循这些指南，可以确保 uni-app x 项目的样式代码稳定、可维护且具有良好的跨平台兼容性。
