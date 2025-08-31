# Flyer Popup 底部弹窗组件

基于 uni-app x 实现的底部弹窗组件，采用全局 store 管理状态，支持自定义样式和多实例管理。

## 特性

- 🎯 基于 uni-app x 和 UTS 语言开发
- 📱 支持 APP-Android、APP-iOS 平台
- 🚀 使用 Vue 3 组合式 API
- 💪 全局响应式状态管理，支持多实例
- 🎨 灵活的样式配置和自定义选项
- 📐 自动适配 iPhone X 及以上设备的安全区域
- 🔧 支持点击遮罩关闭、自定义背景等配置

## 快速开始

### 1. 组件引入

组件已支持 easycom 自动注册，无需手动引入：

```vue
<template>
  <!-- 在需要使用的页面引入组件 -->
  <flyer-popup>
    <!-- 弹窗内容 -->
    <view class="popup-content">
      <text>这是底部弹窗内容</text>
    </view>
  </flyer-popup>
</template>
```

### 2. 基础用法

```vue
<script setup lang="uts">
import { mutations as popupMutations } from '@/components/flyer-popup/store.uts'

// 显示底部弹窗
async function showPopup() {
  try {
    const result = await popupMutations.show({
      background: '#ffffff',
      radius: 24,
      maskClosable: true
    })
    console.log('弹窗关闭，结果:', result)
  } catch (error) {
    console.log('弹窗被取消或关闭')
  }
}

// 隐藏弹窗
function hidePopup() {
  popupMutations.hide()
}
</script>
```

## API 文档

### mutations 方法

#### show(config?: PopupConfig): Promise<boolean>

显示底部弹窗的基础方法

**参数：**

```typescript
type PopupConfig = {
  background?: string      // 背景颜色，默认：'#ffffff'
  radius?: number          // 圆角大小(rpx)，默认：24
  zIndex?: number          // 层级，默认：996
  id?: string              // 目标组件ID，用于多实例管理
  maskClosable?: boolean   // 点击遮罩是否可关闭，默认：true
  maskBackground?: string  // 遮罩背景颜色，默认：'rgba(0, 0, 0, 0.6)'
  safeArea?: boolean       // 是否适配安全区域，默认：true
}
```

**返回值：**
- `Promise<boolean>` - 用户操作结果，`true` 表示确认，`false` 表示取消或隐藏

#### hide()

隐藏弹窗（返回 false 结果）

#### close()

关闭弹窗（抛出 'closed' 异常）

#### confirm() / cancel()

手动控制弹窗状态的方法（一般不需要直接调用）

### 组件 Props

#### id?: string

组件实例标识，用于多实例管理。

#### background?: string

弹窗背景颜色，覆盖 store 中的全局设置。

#### radius?: number

弹窗圆角大小(rpx)，覆盖 store 中的全局设置。

#### safeArea?: boolean

是否适配底部安全区域，覆盖 store 中的全局设置。

#### maskBackground?: string

遮罩背景颜色，覆盖 store 中的全局设置。

## 使用示例

### 基础底部弹窗

```vue
<template>
  <flyer-popup>
    <view class="popup-content">
      <view class="popup-header">
        <text class="popup-title">选择操作</text>
      </view>
      <view class="popup-body">
        <view class="popup-item" @click="handleEdit">
          <text class="popup-item-text">编辑</text>
        </view>
        <view class="popup-item" @click="handleDelete">
          <text class="popup-item-text">删除</text>
        </view>
        <view class="popup-item" @click="handleShare">
          <text class="popup-item-text">分享</text>
        </view>
      </view>
      <view class="popup-footer">
        <view class="popup-cancel-btn" @click="handleCancel">
          <text class="popup-cancel-text">取消</text>
        </view>
      </view>
    </view>
  </flyer-popup>
</template>

<script setup lang="uts">
import { mutations as popupMutations } from '@/components/flyer-popup/store.uts'

async function showActionSheet() {
  try {
    await popupMutations.show({
      maskClosable: true,
      safeArea: true
    })
  } catch (error) {
    console.log('用户取消操作')
  }
}

function handleEdit() {
  popupMutations.confirm()
  console.log('用户选择编辑')
}

function handleDelete() {
  popupMutations.confirm()
  console.log('用户选择删除')
}

function handleShare() {
  popupMutations.confirm()
  console.log('用户选择分享')
}

function handleCancel() {
  popupMutations.cancel()
}
</script>
```

### 自定义样式弹窗

```vue
<script setup lang="uts">
async function showCustomPopup() {
  const result = await popupMutations.show({
    background: '#f8f9fa',
    radius: 32,
    maskBackground: 'rgba(0, 0, 0, 0.8)',
    maskClosable: false,
    zIndex: 1000
  })
  
  console.log('自定义弹窗结果：', result)
}
</script>
```

### 多实例管理

```vue
<template>
  <!-- 为不同的弹窗指定不同的 id -->
  <flyer-popup id="main-popup" />
  <flyer-popup id="sub-popup" />
</template>

<script setup lang="uts">
async function showMainPopup() {
  // 只在 main-popup 实例中显示
  await popupMutations.show({
    id: 'main-popup',
    background: '#ffffff'
  })
}

async function showSubPopup() {
  // 只在 sub-popup 实例中显示
  await popupMutations.show({
    id: 'sub-popup',
    background: '#f0f0f0'
  })
}
</script>
```

### 内容滚动弹窗

```vue
<template>
  <flyer-popup>
    <!-- #ifdef APP -->
    <scroll-view class="popup-scroll" scroll-y="true">
    <!-- #endif -->
      <view class="popup-content">
        <view class="popup-header">
          <text class="popup-title">长内容弹窗</text>
        </view>
        <view class="popup-body">
          <view v-for="(item, index) in longList" :key="index" class="list-item">
            <text class="list-item-text">{{ item }}</text>
          </view>
        </view>
      </view>
    <!-- #ifdef APP -->
    </scroll-view>
    <!-- #endif -->
  </flyer-popup>
</template>

<style>
.popup-scroll {
  max-height: 500px;
}

.popup-content {
  padding: 20px;
}

.popup-header {
  padding-bottom: 16px;
  border-bottom-width: 1px;
  border-bottom-color: #ebedf0;
}

.popup-title {
  font-size: 16px;
  font-weight: 600;
  color: #323233;
  text-align: center;
}

.popup-body {
  padding-top: 16px;
}

.list-item {
  padding: 12px 0;
  border-bottom-width: 1px;
  border-bottom-color: #f7f8fa;
}

.list-item-text {
  font-size: 14px;
  color: #646566;
}
</style>
```

## 样式自定义

组件使用标准的 CSS 类名，可以通过全局样式进行自定义：

```css
/* 自定义弹窗样式 */
.flyer-popup-overlay {
  background-color: rgba(0, 0, 0, 0.8);
}

.flyer-popup-modal {
  background-color: #ffffff;
  border-top-left-radius: 32rpx;
  border-top-right-radius: 32rpx;
}

/* 自定义内容样式 */
.popup-content {
  padding: 24px;
}

.popup-item {
  height: 48px;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  border-bottom-width: 1px;
  border-bottom-color: #ebedf0;
}

.popup-item-text {
  font-size: 16px;
  color: #323233;
}

.popup-cancel-btn {
  height: 48px;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  margin-top: 8px;
  background-color: #ffffff;
  border-radius: 8px;
}

.popup-cancel-text {
  font-size: 16px;
  color: #969799;
}
```

## 注意事项

1. **UTS 语法**：组件使用 UTS 语言开发，请注意类型安全和语法规范
2. **Promise 处理**：所有弹窗方法都返回 Promise，建议使用 async/await 语法
3. **组件引入**：需要在使用的页面中引入 `<flyer-popup />` 组件
4. **平台支持**：目前支持 APP-Android 和 APP-iOS 平台
5. **全局状态**：多个页面可以共享同一个弹窗状态，注意状态管理
6. **安全区域**：在 iPhone X 及以上设备会自动适配底部安全区域
7. **内容滚动**：长内容需要使用 scroll-view 组件包装并添加条件编译

## 更新日志

### v1.0.0
- 🎉 初始版本发布
- ✨ 支持底部弹窗显示和隐藏
- 🎨 美观的 UI 设计和动画效果
- 🔧 灵活的配置选项
- 📱 自动适配移动端安全区域
- 🏪 全局状态管理支持多实例
