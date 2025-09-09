---
mode: agent
---
# uni-app x 样式开发指南

基于 Flyer UI 项目实践的 CSS 样式开发规范，涵盖 uni-app x 特有的样式约束和最佳实践。

## 🎨 CSS 基础规范

### 1. 支持的 CSS 特性
```css
/* ✅ 支持的选择器 */
.class-name { }           /* 类选择器 - 推荐 */
#id-name { }             /* ID 选择器 - 可用但不推荐 */
view { }                 /* 标签选择器 - 基础组件可用 */

/* ❌ 不支持的选择器 */
.parent > .child { }     /* 子选择器 */
.element + .sibling { }  /* 相邻选择器 */
.element ~ .sibling { }  /* 通用兄弟选择器 */
.class:hover { }         /* 伪类选择器 */
.class:active { }        /* 伪类选择器 */
.class::before { }       /* 伪元素选择器 */
.class::after { }        /* 伪元素选择器 */
```

### 2. 支持的单位系统
```css
/* ✅ 支持的单位 */
.container {
  width: 750rpx;         /* rpx - 推荐，响应式像素 */
  height: 200px;         /* px - 固定像素 */
  margin: 50%;           /* % - 百分比 */
}

/* ❌ 不支持的单位 */
.element {
  width: 100vw;          /* 视窗单位不支持 */
  height: 100vh;         /* 视窗单位不支持 */
  font-size: 1.2em;      /* em 单位不支持 */
  padding: 2rem;         /* rem 单位不支持 */
  margin: 10vmin;        /* vmin/vmax 不支持 */
}
```

### 3. 文字样式限制
```css
/* ✅ 文字样式仅适用于特定标签 */
text, button, input, textarea {
  font-size: 32rpx;      /* 字体大小 */
  color: #333333;        /* 文字颜色 */
  text-align: center;    /* 文字对齐 */
  font-weight: bold;     /* 字体粗细: normal|bold|400|700 */
  line-height: 1.5;      /* 行高 (仅 text、button、textarea) */
  white-space: nowrap;   /* 空白处理 (仅 text、button) */
}

/* ❌ 错误：在 view 上使用文字样式 */
view {
  font-size: 32rpx;      /* 无效 */
  color: #333333;        /* 无效 */
  text-align: center;    /* 无效 */
}
```

## 🏗️ 样式架构设计

### 1. BEM 命名规范
```css
/* 块 (Block) - 组件根元素 */
.flyer-actionsheet { }

/* 元素 (Element) - 组件内部元素 */
.flyer-actionsheet__mask { }
.flyer-actionsheet__wrap { }
.flyer-actionsheet__title { }
.flyer-actionsheet__content { }
.flyer-actionsheet__btn { }
.flyer-actionsheet__cancel { }

/* 修饰符 (Modifier) - 状态或变体 */
.flyer-actionsheet--show { }
.flyer-actionsheet--dark { }
.flyer-actionsheet__btn--primary { }
.flyer-actionsheet__btn--disabled { }

/* 实际应用示例 */
.flyer-actionsheet__btn {
  height: 100rpx;
  line-height: 100rpx;
  background-color: #FFFFFF;
  border-bottom: 1px solid #EEEEEE;
}

.flyer-actionsheet__btn--primary {
  background-color: #007AFF;
  color: #FFFFFF;
}

.flyer-actionsheet__btn--disabled {
  opacity: 0.6;
  pointer-events: none;
}
```

### 2. 主题色彩系统
```css
/* 基础色彩变量 (通过 CSS 类实现) */
.flyer-color-primary { color: #007AFF; }
.flyer-color-success { color: #34C759; }
.flyer-color-warning { color: #FF9500; }
.flyer-color-danger { color: #FF3B30; }
.flyer-color-info { color: #5AC8FA; }

.flyer-bg-primary { background-color: #007AFF; }
.flyer-bg-success { background-color: #34C759; }
.flyer-bg-warning { background-color: #FF9500; }
.flyer-bg-danger { background-color: #FF3B30; }
.flyer-bg-info { background-color: #5AC8FA; }

/* 中性色系 */
.flyer-text-primary { color: #333333; }
.flyer-text-secondary { color: #666666; }
.flyer-text-placeholder { color: #999999; }
.flyer-text-disabled { color: #CCCCCC; }

.flyer-bg-light { background-color: #F8F8F8; }
.flyer-bg-white { background-color: #FFFFFF; }
.flyer-bg-dark { background-color: #222222; }
```

### 3. 响应式设计系统
```css
/* 基础栅格系统 */
.flyer-container {
  width: 100%;
  padding: 0 30rpx;
  box-sizing: border-box;
}

.flyer-row {
  display: flex;
  flex-wrap: wrap;
  margin: 0 -15rpx;
}

.flyer-col {
  padding: 0 15rpx;
  box-sizing: border-box;
}

/* 列宽系统 */
.flyer-col-1 { flex: 0 0 8.333333%; width: 8.333333%; }
.flyer-col-2 { flex: 0 0 16.666667%; width: 16.666667%; }
.flyer-col-3 { flex: 0 0 25%; width: 25%; }
.flyer-col-4 { flex: 0 0 33.333333%; width: 33.333333%; }
.flyer-col-6 { flex: 0 0 50%; width: 50%; }
.flyer-col-8 { flex: 0 0 66.666667%; width: 66.666667%; }
.flyer-col-12 { flex: 0 0 100%; width: 100%; }

/* 间距系统 */
.flyer-m-0 { margin: 0; }
.flyer-m-1 { margin: 8rpx; }
.flyer-m-2 { margin: 16rpx; }
.flyer-m-3 { margin: 24rpx; }
.flyer-m-4 { margin: 32rpx; }

.flyer-p-0 { padding: 0; }
.flyer-p-1 { padding: 8rpx; }
.flyer-p-2 { padding: 16rpx; }
.flyer-p-3 { padding: 24rpx; }
.flyer-p-4 { padding: 32rpx; }
```

## 🎪 动画与过渡效果

### 1. 标准过渡动画
```css
/* 淡入淡出 */
.flyer-fade-enter,
.flyer-fade-leave-to {
  opacity: 0;
}

.flyer-fade-enter-active,
.flyer-fade-leave-active {
  transition: opacity 0.3s ease;
}

/* 滑动进入 (底部) */
.flyer-slide-up-enter,
.flyer-slide-up-leave-to {
  transform: translate3d(0, 100%, 0);
}

.flyer-slide-up-enter-active,
.flyer-slide-up-leave-active {
  transition: transform 0.25s ease-in-out;
}

/* 缩放动画 */
.flyer-scale-enter,
.flyer-scale-leave-to {
  transform: scale(0.8);
  opacity: 0;
}

.flyer-scale-enter-active,
.flyer-scale-leave-active {
  transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
}
```

### 2. 组件状态动画
```css
/* ActionSheet 动画 */
.flyer-actionsheet__mask {
  transition: opacity 0.3s ease-in-out;
  opacity: 0;
  visibility: hidden;
}

.flyer-actionsheet__mask--show {
  opacity: 1;
  visibility: visible;
}

.flyer-actionsheet__wrap {
  transform: translate3d(0, 100%, 0);
  transition: transform 0.25s ease-in-out;
  visibility: hidden;
}

.flyer-actionsheet__wrap--show {
  transform: translate3d(0, 0, 0);
  visibility: visible;
}

/* 按钮点击反馈 */
.flyer-btn {
  transition: all 0.15s ease;
}

.flyer-btn:active {
  transform: scale(0.98);
  opacity: 0.8;
}
```

### 3. 加载动画
```css
/* 旋转加载 */
@keyframes flyer-spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.flyer-loading {
  animation: flyer-spin 1s linear infinite;
}

/* 脉冲动画 */
@keyframes flyer-pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.5; }
}

.flyer-pulse {
  animation: flyer-pulse 1.5s ease-in-out infinite;
}

/* 弹性动画 */
@keyframes flyer-bounce {
  0%, 20%, 53%, 80%, 100% {
    transform: translate3d(0, 0, 0);
  }
  40%, 43% {
    transform: translate3d(0, -20rpx, 0);
  }
  70% {
    transform: translate3d(0, -10rpx, 0);
  }
  90% {
    transform: translate3d(0, -4rpx, 0);
  }
}

.flyer-bounce {
  animation: flyer-bounce 1s ease;
}
```

## 📱 布局设计模式

### 1. Flexbox 布局规范
```css
/* 水平居中布局 */
.flyer-flex-center {
  display: flex;
  justify-content: center;
  align-items: center;
}

/* 垂直分布布局 */
.flyer-flex-column {
  display: flex;
  flex-direction: column;
}

/* 空间分布布局 */
.flyer-flex-between {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.flyer-flex-around {
  display: flex;
  justify-content: space-around;
  align-items: center;
}

/* 自适应布局 */
.flyer-flex-1 { flex: 1; }
.flyer-flex-2 { flex: 2; }
.flyer-flex-3 { flex: 3; }

/* 实际组件应用 */
.flyer-actionsheet__operate-box {
  display: flex;
  flex-direction: column;
  padding-bottom: 12rpx;
}

.flyer-actionsheet__btn {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100rpx;
}
```

### 2. 定位布局规范
```css
/* 固定定位 (弹窗常用) */
.flyer-fixed-full {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
}

.flyer-fixed-top {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
}

.flyer-fixed-bottom {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
}

/* 绝对定位 */
.flyer-absolute-center {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

/* 层级管理 */
.flyer-z-mask { z-index: 999; }
.flyer-z-popup { z-index: 1000; }
.flyer-z-modal { z-index: 1100; }
.flyer-z-toast { z-index: 1200; }
```

### 3. 安全区域适配
```css
/* iOS 安全区域 */
.flyer-safe-area-top {
  padding-top: 44px; /* 状态栏高度 */
}

.flyer-safe-area-bottom {
  padding-bottom: 34px; /* iPhone X 系列底部安全区域 */
}

/* 条件安全区域 */
.flyer-safe-area-inset {
  padding-top: constant(safe-area-inset-top);
  padding-bottom: constant(safe-area-inset-bottom);
  padding-left: constant(safe-area-inset-left);
  padding-right: constant(safe-area-inset-right);
  
  /* iOS 11+ */
  padding-top: env(safe-area-inset-top);
  padding-bottom: env(safe-area-inset-bottom);
  padding-left: env(safe-area-inset-left);
  padding-right: env(safe-area-inset-right);
}

/* 实际应用 */
.flyer-actionsheet__safe-bottom {
  height: 168rpx;
  padding-bottom: 34px;
}
```

## 🎨 内联样式处理

### 1. 字符串拼接规范
```vue
<template>
  <!-- ✅ 正确的内联样式写法 -->
  <text :style="'color:' + textColor + ';font-size:' + fontSize + 'rpx'">
    文本内容
  </text>
  
  <view :style="'background-color:' + bgColor + ';padding:' + padding + 'rpx'">
    容器内容
  </view>
  
  <!-- ✅ 复杂样式拼接 -->
  <view :style="'width:' + width + 'rpx;height:' + height + 'rpx;border-radius:' + radius + 'rpx;background:linear-gradient(' + gradientAngle + 'deg,' + startColor + ',' + endColor + ')'">
    渐变背景
  </view>
  
  <!-- ❌ 错误：对象语法不支持 -->
  <text :style="{ color: textColor, fontSize: fontSize + 'rpx' }">
    错误语法
  </text>
</template>

<script lang="uts">
export default {
  computed: {
    // ✅ 推荐：使用计算属性管理复杂样式
    buttonStyle(): string {
      let style = 'height:' + this.height + 'rpx;'
      style += 'background-color:' + this.backgroundColor + ';'
      style += 'color:' + this.textColor + ';'
      style += 'border-radius:' + this.borderRadius + 'rpx;'
      
      if (this.disabled) {
        style += 'opacity:0.6;'
      }
      
      return style
    }
  }
}
</script>
```

### 2. 动态样式管理
```uts
// 样式计算方法
methods: {
  getItemStyle(item: UTSJSONObject, index: number): string {
    let style = ''
    
    // 基础样式
    style += 'padding:' + this.itemPadding + 'rpx;'
    
    // 动态颜色
    const color = item['color'] as string
    if (color != null && color.length > 0) {
      style += 'color:' + color + ';'
    }
    
    // 动态字体大小
    const size = item['size'] as number
    if (size != null && size > 0) {
      style += 'font-size:' + size + 'rpx;'
    }
    
    // 边框样式
    if (index < this.itemList.length - 1) {
      style += 'border-bottom:1px solid #EEEEEE;'
    }
    
    return style
  },
  
  // 主题样式计算
  getThemeStyle(): string {
    const theme = this.theme || 'light'
    let style = ''
    
    if (theme === 'dark') {
      style += 'background-color:#222222;color:#FFFFFF;'
    } else {
      style += 'background-color:#FFFFFF;color:#333333;'
    }
    
    return style
  }
}
```

## 🔧 样式工具类

### 1. 通用工具类
```css
/* 显示隐藏 */
.flyer-show { display: flex; }
.flyer-hide { display: none; }
.flyer-invisible { visibility: hidden; }

/* 文字处理 */
.flyer-text-ellipsis {
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}

.flyer-text-break {
  word-break: break-all;
  word-wrap: break-word;
}

/* 清除默认样式 */
.flyer-no-border { border: none; }
.flyer-no-outline { outline: none; }
.flyer-no-background { background: none; }

/* 圆角 */
.flyer-radius-small { border-radius: 8rpx; }
.flyer-radius-medium { border-radius: 12rpx; }
.flyer-radius-large { border-radius: 16rpx; }
.flyer-radius-round { border-radius: 50%; }

/* 阴影 */
.flyer-shadow-small {
  box-shadow: 0 2rpx 8rpx rgba(0, 0, 0, 0.1);
}

.flyer-shadow-medium {
  box-shadow: 0 4rpx 16rpx rgba(0, 0, 0, 0.15);
}

.flyer-shadow-large {
  box-shadow: 0 8rpx 32rpx rgba(0, 0, 0, 0.2);
}
```

### 2. 状态样式类
```css
/* 交互状态 */
.flyer-clickable {
  cursor: pointer;
  transition: all 0.15s ease;
}

.flyer-clickable:active {
  transform: scale(0.98);
  opacity: 0.8;
}

.flyer-disabled {
  opacity: 0.6;
  pointer-events: none;
  cursor: not-allowed;
}

/* 加载状态 */
.flyer-loading {
  position: relative;
  pointer-events: none;
}

.flyer-loading::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  width: 40rpx;
  height: 40rpx;
  margin: -20rpx 0 0 -20rpx;
  border: 4rpx solid #E5E5E5;
  border-top-color: #007AFF;
  border-radius: 50%;
  animation: flyer-spin 1s linear infinite;
}
```

## 📋 样式开发检查清单

### 基础规范
- [ ] 只使用类选择器，避免复杂选择器
- [ ] 单位使用 rpx (响应式) 或 px (固定)，避免 vh/vw/em/rem
- [ ] 文字样式只应用于 text/button/input/textarea
- [ ] 内联样式使用字符串拼接，不使用对象语法

### 命名规范
- [ ] 类名使用 BEM 规范 (block__element--modifier)
- [ ] 组件前缀统一 (flyer-)
- [ ] 状态类命名清晰 (--show, --active, --disabled)

### 布局设计
- [ ] 使用 Flexbox 进行布局
- [ ] 合理使用定位 (fixed/absolute)
- [ ] 层级管理清晰 (z-index)
- [ ] 安全区域适配完整

### 响应式设计
- [ ] 使用 rpx 实现响应式
- [ ] 考虑不同屏幕尺寸
- [ ] 横竖屏适配测试

### 动画效果
- [ ] 使用 CSS 过渡而非 JS 动画
- [ ] 动画时长合理 (200-300ms)
- [ ] 提供平滑的用户反馈

### 兼容性
- [ ] iOS 平台测试
- [ ] Android 平台测试
- [ ] 不同设备测试

## 💡 样式最佳实践

1. **性能优化**：优先使用 CSS 类而非内联样式
2. **维护性**：使用计算属性管理复杂动态样式  
3. **一致性**：建立统一的设计系统和工具类
4. **响应式**：使用 rpx 确保在不同设备上的一致性
5. **可读性**：遵循 BEM 命名规范，提高代码可读性
6. **扩展性**：设计模块化的样式架构，便于后续扩展

遵循这些样式开发指南，可以确保项目样式的质量、性能和维护性。
