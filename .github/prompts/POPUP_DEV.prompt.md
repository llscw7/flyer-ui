---
mode: agent
---
# uni-app x 弹窗类组件开发指南

基于 `flyer-actionsheet.uvue` 实践总结的弹窗、模态框、抽屉等弹出式组件开发专项指南。

## 🎯 弹窗组件核心模式

### 1. 标准弹窗结构
```vue
<template>
  <!-- 遮罩层 -->
  <view 
    class="component-mask" 
    :class="{ 'component-mask--show': shouldShow }"
    @tap="handleMaskClick">
  </view>
  
  <!-- 内容容器 -->
  <view 
    class="component-wrap" 
    :class="{ 'component-wrap--show': shouldShow }"
    :style="{ zIndex: zIndex }">
    
    <!-- 标题区域 -->
    <text class="component-title" v-if="title">{{ title }}</text>
    
    <!-- 内容区域 -->
    <view class="component-content">
      <slot></slot>
    </view>
    
    <!-- 操作区域 -->
    <view class="component-actions">
      <text @tap="handleCancel" v-if="showCancel">取消</text>
      <text @tap="handleConfirm" v-if="showConfirm">确定</text>
    </view>
  </view>
</template>
```

### 2. 状态管理标准模式
```uts
export default {
  name: 'FlyerModal',
  props: {
    /**
     * 显示状态 - 必须由父组件控制
     */
    visible: {
      type: Boolean,
      required: true
    },
    
    /**
     * 状态设置函数 - 用于更新 visible 状态
     */
    setVisible: {
      type: Function as PropType<(visible: boolean) => void>,
      required: true
    },
    
    /**
     * 层级控制
     */
    zIndex: {
      type: Number,
      default: 1000
    },
    
    /**
     * 点击遮罩是否可关闭
     */
    maskClosable: {
      type: Boolean,
      default: true
    }
  },
  
  computed: {
    /**
     * 计算是否应该显示 - 统一入口
     */
    shouldShow(): boolean {
      return this.visible
    }
  },
  
  methods: {
    /**
     * 遮罩点击处理
     */
    handleMaskClick(): void {
      if (this.maskClosable) {
        this.handleCancel()
      }
    },
    
    /**
     * 取消操作 - 标准关闭流程
     */
    handleCancel(): void {
      this.setVisible(false)
      this.$emit('cancel')
    },
    
    /**
     * 确认操作 - 不自动关闭，由父组件决定
     */
    handleConfirm(): void {
      this.$emit('confirm')
      // 注意：不要在这里调用 setVisible(false)
      // 让父组件根据确认结果决定是否关闭
    }
  }
}
```

## 🎨 弹窗动画与样式

### 1. 标准动画效果
```css
/* 遮罩层动画 */
.component-mask {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.3);
  opacity: 0;
  visibility: hidden;
  transition: opacity 0.3s ease-in-out;
  z-index: 999;
}

.component-mask--show {
  opacity: 1;
  visibility: visible;
}

/* 内容容器动画 */
.component-wrap {
  position: fixed;
  width: 100%;
  min-height: 100rpx;
  visibility: hidden;
  transition: all 0.25s ease-in-out;
  z-index: 1000;
}

/* 底部弹出动画 (ActionSheet 风格) */
.component-wrap--bottom {
  left: 0;
  right: 0;
  bottom: 0;
  transform: translate3d(0, 100%, 0);
  border-top-left-radius: 24rpx;
  border-top-right-radius: 24rpx;
}

.component-wrap--bottom.component-wrap--show {
  transform: translate3d(0, 0, 0);
  visibility: visible;
}

/* 中间弹出动画 (Modal 风格) */
.component-wrap--center {
  top: 50%;
  left: 50%;
  transform: translate3d(-50%, -50%, 0) scale(0.8);
  border-radius: 16rpx;
  width: 600rpx;
}

.component-wrap--center.component-wrap--show {
  transform: translate3d(-50%, -50%, 0) scale(1);
  visibility: visible;
}

/* 侧边滑出动画 (Drawer 风格) */
.component-wrap--right {
  top: 0;
  right: 0;
  bottom: 0;
  width: 600rpx;
  transform: translate3d(100%, 0, 0);
}

.component-wrap--right.component-wrap--show {
  transform: translate3d(0, 0, 0);
  visibility: visible;
}
```

### 2. 安全区域适配
```css
/* 底部安全区域适配 */
.component-wrap--bottom {
  padding-bottom: 0;
}

.component-wrap--safe-area {
  padding-bottom: 34px; /* iPhone X 系列安全区域 */
}

/* 状态栏适配 */
.component-wrap--full {
  padding-top: 44px; /* 状态栏高度 */
}
```

```uts
// 安全区域检测逻辑
computed: {
  needSafeArea(): boolean {
    return this.safeArea && this.isLastItem()
  },
  
  wrapClass(): string {
    let classes = 'component-wrap component-wrap--bottom'
    if (this.shouldShow) {
      classes += ' component-wrap--show'
    }
    if (this.needSafeArea) {
      classes += ' component-wrap--safe-area'
    }
    return classes
  }
}
```

## 📋 常见弹窗类型实现

### 1. ActionSheet (操作面板)
```uts
// 基于 flyer-actionsheet.uvue 的标准实现
export default {
  name: 'FlyerActionsheet',
  props: {
    visible: { type: Boolean, required: true },
    setVisible: { type: Function, required: true },
    itemList: { type: Array as PropType<UTSJSONObject[]>, required: true },
    tips: { type: String, default: '' },
    isCancel: { type: Boolean, default: true },
    maskClosable: { type: Boolean, default: true }
  },
  
  methods: {
    handleItemClick(index: number): void {
      const item = this.itemList[index] as UTSJSONObject
      
      // 检查项目是否禁用
      const disabled = item['disabled'] as boolean
      if (disabled == true) return
      
      // 发送点击事件
      this.$emit('click', {
        index: index,
        item: item,
        text: item['text'] as string,
        value: item['value'] as string
      })
      
      // 可选：自动关闭
      if (this.autoClose !== false) {
        this.setVisible(false)
      }
    }
  }
}
```

### 2. Modal (模态框)
```uts
export default {
  name: 'FlyerModal',
  props: {
    visible: { type: Boolean, required: true },
    setVisible: { type: Function, required: true },
    title: { type: String, default: '' },
    content: { type: String, default: '' },
    showCancel: { type: Boolean, default: true },
    showConfirm: { type: Boolean, default: true },
    cancelText: { type: String, default: '取消' },
    confirmText: { type: String, default: '确定' }
  },
  
  methods: {
    handleConfirm(): void {
      this.$emit('confirm', {
        timestamp: Date.now()
      })
    }
  }
}
```

### 3. Popup (通用弹窗)
```uts
export default {
  name: 'FlyerPopup',
  props: {
    visible: { type: Boolean, required: true },
    setVisible: { type: Function, required: true },
    position: { 
      type: String, 
      default: 'bottom' // top | bottom | center | left | right
    },
    width: { type: String, default: 'auto' },
    height: { type: String, default: 'auto' },
    round: { type: Boolean, default: true }
  },
  
  computed: {
    positionClass(): string {
      return `flyer-popup--${this.position}`
    },
    
    customStyle(): string {
      let style = ''
      if (this.width !== 'auto') {
        style += `width:${this.width};`
      }
      if (this.height !== 'auto') {
        style += `height:${this.height};`
      }
      return style
    }
  }
}
```

### 4. Drawer (抽屉)
```uts
export default {
  name: 'FlyerDrawer',
  props: {
    visible: { type: Boolean, required: true },
    setVisible: { type: Function, required: true },
    position: { type: String, default: 'right' }, // left | right
    width: { type: String, default: '600rpx' },
    showOverlay: { type: Boolean, default: true }
  },
  
  computed: {
    drawerStyle(): string {
      return `width:${this.width};`
    }
  }
}
```

## 🔧 高级功能实现

### 1. 多层弹窗管理
```uts
// 全局弹窗层级管理
const POPUP_Z_INDEX = {
  base: 1000,
  increment: 10
}

let currentZIndex = POPUP_Z_INDEX.base

export default {
  props: {
    zIndex: {
      type: Number,
      default: () => {
        currentZIndex += POPUP_Z_INDEX.increment
        return currentZIndex
      }
    }
  },
  
  beforeUnmount(): void {
    // 组件销毁时释放层级
    if (this.zIndex > POPUP_Z_INDEX.base) {
      currentZIndex = this.zIndex - POPUP_Z_INDEX.increment
    }
  }
}
```

### 2. 键盘避让
```uts
data() {
  return {
    keyboardHeight: 0
  }
},

computed: {
  contentStyle(): string {
    let style = ''
    if (this.keyboardHeight > 0) {
      style += `padding-bottom:${this.keyboardHeight}px;`
    }
    return style
  }
},

mounted(): void {
  // 监听键盘弹出
  uni.onKeyboardHeightChange((res) => {
    this.keyboardHeight = res.height
  })
}
```

### 3. 手势关闭
```uts
data() {
  return {
    startY: 0,
    currentY: 0,
    isDragging: false
  }
},

methods: {
  handleTouchStart(event: any): void {
    this.startY = event.touches[0].clientY
    this.isDragging = true
  },
  
  handleTouchMove(event: any): void {
    if (!this.isDragging) return
    
    this.currentY = event.touches[0].clientY
    const deltaY = this.currentY - this.startY
    
    // 只允许向下滑动
    if (deltaY > 0) {
      // 更新位置（可选：添加阻尼效果）
      const translateY = Math.min(deltaY, 200)
      // 应用变换...
    }
  },
  
  handleTouchEnd(): void {
    if (!this.isDragging) return
    
    const deltaY = this.currentY - this.startY
    
    // 滑动距离超过阈值则关闭
    if (deltaY > 100) {
      this.handleCancel()
    } else {
      // 回弹到原位置
      // 重置变换...
    }
    
    this.isDragging = false
  }
}
```

## 📱 平台适配指南

### 1. iOS 适配
```css
/* iOS 安全区域 */
.flyer-popup--ios {
  padding-top: constant(safe-area-inset-top);
  padding-bottom: constant(safe-area-inset-bottom);
  padding-top: env(safe-area-inset-top);
  padding-bottom: env(safe-area-inset-bottom);
}

/* iOS 状态栏适配 */
.flyer-popup__header--ios {
  padding-top: 44px;
}
```

### 2. Android 适配
```css
/* Android 导航栏适配 */
.flyer-popup--android {
  padding-bottom: 48px; /* 导航栏高度 */
}

/* Android 沉浸式状态栏 */
.flyer-popup__header--android {
  padding-top: 24px;
}
```

### 3. 条件编译
```uts
// 平台特定逻辑
// #ifdef APP-IOS
const SAFE_AREA_BOTTOM = 34
// #endif

// #ifdef APP-ANDROID  
const SAFE_AREA_BOTTOM = 0
// #endif

computed: {
  safeAreaStyle(): string {
    // #ifdef APP-IOS
    return 'padding-bottom:34px;'
    // #endif
    
    // #ifdef APP-ANDROID
    return ''
    // #endif
  }
}
```

## 🧪 弹窗组件测试

### 1. 基础功能测试
```uts
// 测试用例
export default {
  name: 'PopupTest',
  data() {
    return {
      visible: false,
      testResults: [] as string[]
    }
  },
  
  methods: {
    // 测试显示隐藏
    testShowHide(): void {
      this.visible = true
      setTimeout(() => {
        this.visible = false
        this.addResult('✓ 显示隐藏测试通过')
      }, 1000)
    },
    
    // 测试遮罩关闭
    testMaskClose(): void {
      this.visible = true
      // 模拟点击遮罩
      setTimeout(() => {
        this.handleMaskClick()
        if (!this.visible) {
          this.addResult('✓ 遮罩关闭测试通过')
        }
      }, 500)
    },
    
    // 测试快速操作
    testRapidOperations(): void {
      for (let i = 0; i < 10; i++) {
        setTimeout(() => {
          this.visible = !this.visible
        }, i * 100)
      }
      
      setTimeout(() => {
        this.addResult('✓ 快速操作测试完成')
      }, 1200)
    },
    
    addResult(result: string): void {
      this.testResults.push(result)
    }
  }
}
```

### 2. 性能测试
```uts
methods: {
  // 渲染性能测试
  testRenderPerformance(): void {
    const startTime = Date.now()
    
    // 创建大量数据
    const largeList: UTSJSONObject[] = []
    for (let i = 0; i < 1000; i++) {
      largeList.push({
        text: `选项 ${i}`,
        value: `option_${i}`
      })
    }
    
    this.itemList = largeList
    this.visible = true
    
    // 测量渲染时间
    this.$nextTick(() => {
      const endTime = Date.now()
      const renderTime = endTime - startTime
      
      console.log(`渲染 1000 项耗时: ${renderTime}ms`)
      
      if (renderTime < 500) {
        this.addResult('✓ 渲染性能测试通过')
      } else {
        this.addResult('⚠ 渲染性能需要优化')
      }
    })
  }
}
```

## 📋 弹窗组件检查清单

### 基础要求
- [ ] 遮罩层正确实现
- [ ] 动画效果流畅
- [ ] 状态管理规范 (visible + setVisible)
- [ ] 事件处理完整 (confirm, cancel)
- [ ] 层级管理正确 (zIndex)

### 用户体验
- [ ] 点击遮罩可关闭 (可配置)
- [ ] 快速操作响应正常
- [ ] 动画时长适中 (200-300ms)
- [ ] 加载状态友好
- [ ] 错误状态处理

### 平台适配
- [ ] iOS 安全区域适配
- [ ] Android 导航栏适配
- [ ] 不同屏幕尺寸测试
- [ ] 横竖屏切换测试

### 性能优化
- [ ] 大量数据渲染优化
- [ ] 内存泄漏检查
- [ ] 动画性能测试
- [ ] 快速开关测试

### 可访问性
- [ ] 键盘导航支持
- [ ] 焦点管理正确
- [ ] 屏幕阅读器友好

## 💡 最佳实践总结

1. **状态管理**: 始终使用 `visible` + `setVisible` 模式
2. **事件设计**: 确认事件不自动关闭，让父组件控制
3. **动画效果**: 使用 CSS 过渡，避免 JS 动画
4. **层级管理**: 合理分配 z-index，避免冲突
5. **平台适配**: 使用条件编译处理平台差异
6. **性能优化**: 避免过度渲染，及时清理资源
7. **用户体验**: 提供直观的交互反馈

遵循这些指南，可以开发出高质量、高性能的弹窗类组件。
