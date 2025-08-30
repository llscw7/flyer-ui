# Flyer Dialog 对话框组件

基于 uni-app x 实现的对话框组件，参考 membership-upgrade 组件的设计模式，通过全局 store 管理状态。

## 特性

- 🎯 基于 uni-app x 和 UTS 语言开发
- 📱 支持 APP-Android、APP-iOS 平台
- 🚀 使用 Vue 3 组合式 API
- 💪 全局响应式状态管理，支持多实例
- 🎨 简洁美观的 UI 设计
- 🔧 灵活的配置选项

## 快速开始

### 1. 组件引入

组件已支持 easycom 自动注册，无需手动引入：

```vue
<template>
  <!-- 在需要使用的页面引入组件 -->
  <flyer-dialog />
</template>
```

### 2. 基础用法

```vue
<script setup lang="uts">
import { mutations as dialogMutations } from '@/components/flyer-dialog/store.uts'

// Alert 提示框
async function showAlert() {
  await dialogMutations.alert('这是一个提示消息')
  console.log('用户点击了确定')
}

// Confirm 确认框
async function showConfirm() {
  const result = await dialogMutations.showConfirm('确定要执行这个操作吗？')
  if (result) {
    console.log('用户点击了确定')
  } else {
    console.log('用户点击了取消')
  }
}
</script>
```

## API 文档

### mutations 方法

#### show(config?: DialogConfig): Promise<boolean>

显示对话框的基础方法

**参数：**

```typescript
type DialogConfig = {
  title?: string        // 标题，默认：'提示'
  content?: string      // 内容文字
  confirmText?: string  // 确定按钮文字，默认：'确定'
  cancelText?: string   // 取消按钮文字，默认：'取消'
  showCancel?: boolean  // 是否显示取消按钮，默认：true
  zIndex?: number       // 层级，默认：9999
  id?: string           // 目标组件ID，用于多实例管理
  maskClosable?: boolean // 点击遮罩是否可关闭，默认：true
}
```

**返回值：**
- `Promise<boolean>` - 用户选择结果，`true` 表示确定，`false` 表示取消

#### alert(content: string, title?: string): Promise<boolean>

显示只有确定按钮的提示框

**参数：**
- `content: string` - 提示内容
- `title?: string` - 标题，可选

#### showConfirm(content: string, title?: string): Promise<boolean>

显示带确定和取消按钮的确认框

**参数：**
- `content: string` - 确认内容
- `title?: string` - 标题，可选

#### confirm() / cancel() / close()

手动控制对话框状态的方法（一般不需要直接调用）

## 使用示例

### 基础提示框

```vue
<script setup lang="uts">
import { mutations as dialogMutations } from '@/components/flyer-dialog/store.uts'

async function handleSave() {
  try {
    // 模拟保存操作
    await saveData()
    await dialogMutations.alert('保存成功！', '操作成功')
  } catch (error) {
    await dialogMutations.alert('保存失败，请重试', '操作失败')
  }
}
</script>
```

### 删除确认

```vue
<script setup lang="uts">
async function handleDelete() {
  const confirmed = await dialogMutations.showConfirm(
    '确定要删除这个项目吗？删除后不可恢复！',
    '删除确认'
  )
  
  if (confirmed) {
    // 执行删除操作
    deleteItem()
  }
}
</script>
```

### 自定义配置

```vue
<script setup lang="uts">
async function showCustomDialog() {
  const result = await dialogMutations.show({
    title: '重要操作',
    content: '这个操作可能会影响系统性能，是否继续？',
    confirmText: '继续执行',
    cancelText: '稍后再试',
    maskClosable: false,  // 禁止点击遮罩关闭
    zIndex: 10000
  })
  
  console.log('用户选择：', result ? '继续执行' : '稍后再试')
}
</script>
```

### 多实例管理

```vue
<template>
  <!-- 为不同的对话框指定不同的 id -->
  <flyer-dialog id="main-dialog" />
  <flyer-dialog id="sub-dialog" />
</template>

<script setup lang="uts">
async function showMainDialog() {
  // 只在 main-dialog 实例中显示
  await dialogMutations.show({
    id: 'main-dialog',
    title: '主对话框',
    content: '这是主对话框的内容'
  })
}

async function showSubDialog() {
  // 只在 sub-dialog 实例中显示
  await dialogMutations.show({
    id: 'sub-dialog',
    title: '子对话框',
    content: '这是子对话框的内容'
  })
}
</script>
```

## 样式自定义

组件使用标准的 CSS 类名，可以通过全局样式进行自定义：

```css
/* 自定义对话框样式 */
.flyer-dialog-modal {
  border-radius: 16px;
}

.flyer-dialog-title {
  color: #333333;
  font-weight: 600;
}

.flyer-dialog-btn-text--confirm {
  color: #007aff;
  font-weight: 500;
}
```

## 注意事项

1. **UTS 语法**：组件使用 UTS 语言开发，请注意类型安全和语法规范
2. **Promise 处理**：所有对话框方法都返回 Promise，建议使用 async/await 语法
3. **组件引入**：需要在使用的页面中引入 `<flyer-dialog />` 组件
4. **平台支持**：目前支持 APP-Android 和 APP-iOS 平台
5. **全局状态**：多个页面可以共享同一个对话框状态，注意状态管理

## 更新日志

### v1.0.0
- 🎉 初始版本发布
- ✨ 支持 alert 和 confirm 对话框
- 🎨 美观的 UI 设计
- 🔧 灵活的配置选项
- 📱 适配移动端界面
