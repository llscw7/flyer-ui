---
mode: agent
---
# uni-app x 组件开发完整指南

基于 Flyer UI 项目实践，总结 uni-app x 组件开发的最佳实践和设计模式。

## 🏗️ 组件架构设计

### 组件分类与设计原则

#### 1. 基础组件 (Basic Components)
```uts
// 示例：Button、Icon、Text 等
// 特点：单一功能、高复用、无复杂状态
export default {
  name: 'FlyerButton',
  props: {
    type: {
      type: String,
      default: 'default' // primary | success | warning | danger
    },
    size: {
      type: String, 
      default: 'medium' // large | medium | small
    },
    disabled: {
      type: Boolean,
      default: false
    },
    loading: {
      type: Boolean,
      default: false
    }
  },
  emits: ['click'],
  methods: {
    handleClick(event: any): void {
      if (this.disabled || this.loading) return
      this.$emit('click', event)
    }
  }
}
```

#### 2. 交互组件 (Interactive Components)  
```uts
// 示例：Modal、Popup、ActionSheet 等
// 特点：状态管理、动画效果、用户交互
export default {
  name: 'FlyerActionsheet',
  props: {
    visible: {
      type: Boolean,
      required: true
    },
    setVisible: {
      type: Function as PropType<(visible: boolean) => void>,
      required: true
    },
    itemList: {
      type: Array as PropType<UTSJSONObject[]>,
      required: true
    },
    maskClosable: {
      type: Boolean,
      default: true
    }
  },
  computed: {
    shouldShow(): boolean {
      return this.visible
    }
  }
}
```

#### 3. 表单组件 (Form Components)
```uts
// 示例：Input、Select、DatePicker 等
// 特点：数据绑定、验证、格式化
export default {
  name: 'FlyerInput',
  props: {
    modelValue: {
      type: String,
      default: ''
    },
    placeholder: {
      type: String,
      default: ''
    },
    disabled: {
      type: Boolean,
      default: false
    },
    maxlength: {
      type: Number,
      default: -1
    }
  },
  emits: ['update:modelValue', 'input', 'focus', 'blur'],
  methods: {
    handleInput(event: any): void {
      const value = event.detail.value
      this.$emit('update:modelValue', value)
      this.$emit('input', event)
    }
  }
}
```

## 📋 Props 设计规范

### 1. Props 类型定义规范
```uts
// ✅ 正确：基础类型 Props
props: {
  title: {
    type: String,
    default: ''
  },
  visible: {
    type: Boolean,
    default: false
  },
  count: {
    type: Number,
    default: 0
  }
}

// ✅ 正确：复杂数组 Props (必须使用 UTSJSONObject[])
props: {
  itemList: {
    type: Array as PropType<UTSJSONObject[]>,
    default: () => []
  },
  options: {
    type: Array as PropType<UTSJSONObject[]>,
    required: true
  }
}

// ✅ 正确：函数 Props
props: {
  setVisible: {
    type: Function as PropType<(visible: boolean) => void>,
    required: true
  },
  onConfirm: {
    type: Function as PropType<(data: UTSJSONObject) => void>,
    default: null
  }
}

// ❌ 错误：自定义类型数组
type MenuItem = { text: string; value: string }
props: {
  items: {
    type: Array as PropType<MenuItem[]>, // 编译错误
    default: () => []
  }
}
```

### 2. Props 命名规范
```uts
// ✅ 推荐的 Props 命名
props: {
  // 布尔类型：使用 is/has/can/should 前缀
  isLoading: Boolean,
  hasIcon: Boolean,
  canClose: Boolean,
  shouldShow: Boolean,
  
  // 回调函数：使用 on 前缀或 set 前缀
  onClick: Function,
  onClose: Function,
  setVisible: Function,
  
  // 样式相关：使用具体名称
  backgroundColor: String,
  textColor: String,
  borderRadius: Number,
  
  // 配置项：使用对象或明确类型
  customStyle: Object,
  itemList: Array,
  
  // 尺寸单位：明确指出单位类型
  fontSize: Number, // rpx
  width: String,    // 支持 rpx/px/%
  height: String    // 支持 rpx/px/%
}
```

### 3. 默认值设计原则
```uts
props: {
  // ✅ 安全的默认值
  type: {
    type: String,
    default: 'default'
  },
  
  // ✅ 对象和数组使用函数返回
  itemList: {
    type: Array as PropType<UTSJSONObject[]>,
    default: () => []
  },
  customStyle: {
    type: Object,
    default: () => ({})
  },
  
  // ✅ 可选的功能默认关闭
  showIcon: {
    type: Boolean,
    default: false
  },
  
  // ✅ 重要功能明确要求传入
  visible: {
    type: Boolean,
    required: true
  }
}
```

## 🎭 事件系统设计

### 1. 事件命名规范
```uts
// ✅ 推荐的事件命名
emits: [
  'click',        // 基础交互事件
  'confirm',      // 确认操作
  'cancel',       // 取消操作
  'close',        // 关闭操作
  'open',         // 打开操作
  'change',       // 值变更
  'input',        // 输入事件
  'focus',        // 获得焦点
  'blur',         // 失去焦点
  'scroll',       // 滚动事件
  'loadmore'      // 加载更多
]

// ❌ 避免的事件命名
emits: [
  'clickBtn',     // 过于具体
  'tap',          // 使用 click 代替
  'touchstart',   // 避免底层事件
  'on-click'      // 不使用连字符
]
```

### 2. 事件数据设计
```uts
methods: {
  // ✅ 事件数据结构规范
  handleItemClick(index: number): void {
    const item = this.itemList[index] as UTSJSONObject
    
    // 传递完整的事件数据
    this.$emit('click', {
      index: index,
      item: item,
      text: item['text'] as string,
      value: item['value'] as string
    })
  },
  
  // ✅ 确认事件数据
  handleConfirm(): void {
    this.$emit('confirm', {
      timestamp: Date.now(),
      data: this.formData,
      valid: this.isValid
    })
  },
  
  // ✅ 取消事件（简单数据）
  handleCancel(): void {
    this.$emit('cancel')
  }
}
```

### 3. 状态管理事件
```uts
// ✅ 组件状态管理模式
props: {
  visible: {
    type: Boolean,
    required: true
  },
  setVisible: {
    type: Function as PropType<(visible: boolean) => void>,
    required: true
  }
},

methods: {
  // 内部状态变更通过 props 中的函数处理
  handleClose(): void {
    this.setVisible(false)
  },
  
  handleOpen(): void {
    this.setVisible(true)
  },
  
  // 同时发送状态变更事件
  handleToggle(): void {
    const newVisible = !this.visible
    this.setVisible(newVisible)
    this.$emit('toggle', newVisible)
  }
}
```

## 🎨 样式设计模式

### 1. CSS 类命名规范 (BEM 风格)
```css
/* ✅ 推荐的类命名 */
.flyer-actionsheet { /* 块 (Block) */ }
.flyer-actionsheet__mask { /* 元素 (Element) */ }
.flyer-actionsheet__wrap { /* 元素 (Element) */ }
.flyer-actionsheet__btn { /* 元素 (Element) */ }
.flyer-actionsheet--show { /* 修饰符 (Modifier) */ }
.flyer-actionsheet__mask--show { /* 元素修饰符 */ }

/* ❌ 避免的类命名 */
.actionsheet { /* 缺少前缀 */ }
.flyer-actionsheet .mask { /* 嵌套选择器 */ }
.flyerActionsheetMask { /* 驼峰命名 */ }
```

### 2. 动态样式处理
```uts
// ✅ 字符串拼接内联样式
template: `
<view :style="'background-color:' + bgColor + ';padding:' + padding + 'rpx'">
<text :style="'color:' + textColor + ';font-size:' + fontSize + 'rpx'">
`

// ✅ 条件样式处理
computed: {
  wrapStyle(): string {
    let style = 'z-index:' + this.zIndex
    if (this.customBg != null) {
      style += ';background-color:' + this.customBg
    }
    return style
  },
  
  btnClass(): string {
    let classes = 'flyer-actionsheet__btn'
    if (this.shouldShow) {
      classes += ' flyer-actionsheet__btn--active'
    }
    return classes
  }
}
```

### 3. 响应式设计模式
```css
/* ✅ 使用 rpx 实现响应式 */
.flyer-actionsheet__wrap {
  width: 100%;
  border-radius: 24rpx;
  padding: 0 30rpx;
}

.flyer-actionsheet__btn {
  height: 100rpx;
  font-size: 32rpx;
  padding: 0 40rpx;
}

/* ✅ 使用百分比布局 */
.flyer-grid__item {
  width: 25%; /* 四列布局 */
  flex: 1;    /* 自适应 */
}

/* ✅ 安全区域适配 */
.flyer-actionsheet__safe {
  height: 168rpx;
  padding-bottom: 34px; /* 固定安全区域高度 */
}
```

## 🔧 组件功能模式

### 1. 弹窗类组件模式
```uts
// 弹窗组件标准结构
export default {
  name: 'FlyerModal',
  props: {
    visible: Boolean,
    setVisible: Function,
    title: String,
    content: String,
    maskClosable: { type: Boolean, default: true },
    showCancel: { type: Boolean, default: true },
    showConfirm: { type: Boolean, default: true }
  },
  
  computed: {
    shouldShow(): boolean {
      return this.visible
    }
  },
  
  methods: {
    handleMaskClick(): void {
      if (this.maskClosable) {
        this.handleCancel()
      }
    },
    
    handleCancel(): void {
      this.setVisible(false)
      this.$emit('cancel')
    },
    
    handleConfirm(): void {
      this.$emit('confirm')
      // 不自动关闭，由父组件控制
    }
  }
}
```

### 2. 列表类组件模式
```uts
// 列表组件标准结构
export default {
  name: 'FlyerList',
  props: {
    itemList: {
      type: Array as PropType<UTSJSONObject[]>,
      default: () => []
    },
    loading: { type: Boolean, default: false },
    hasMore: { type: Boolean, default: true },
    emptyText: { type: String, default: '暂无数据' }
  },
  
  methods: {
    handleItemClick(index: number): void {
      const item = this.itemList[index] as UTSJSONObject
      this.$emit('item-click', {
        index: index,
        item: item
      })
    },
    
    handleLoadMore(): void {
      if (this.loading || !this.hasMore) return
      this.$emit('loadmore')
    },
    
    handleRefresh(): void {
      this.$emit('refresh')
    }
  }
}
```

### 3. 表单类组件模式
```uts
// 表单组件标准结构
export default {
  name: 'FlyerInput',
  props: {
    modelValue: String,
    placeholder: String,
    disabled: Boolean,
    readonly: Boolean,
    maxlength: Number,
    type: { type: String, default: 'text' }
  },
  
  emits: ['update:modelValue', 'input', 'focus', 'blur', 'confirm'],
  
  methods: {
    handleInput(event: any): void {
      if (this.readonly || this.disabled) return
      
      const value = event.detail.value
      this.$emit('update:modelValue', value)
      this.$emit('input', {
        value: value,
        event: event
      })
    },
    
    handleFocus(event: any): void {
      this.$emit('focus', event)
    },
    
    handleBlur(event: any): void {
      this.$emit('blur', event)
    },
    
    handleConfirm(event: any): void {
      this.$emit('confirm', {
        value: event.detail.value,
        event: event
      })
    }
  }
}
```

## 🧪 组件测试规范

### 1. 示例页面结构
```uts
// pages/examples/[component]/[component].uvue
export default {
  name: 'ComponentExample',
  data() {
    return {
      // 基础用法数据
      basicVisible: false,
      basicData: [] as UTSJSONObject[],
      
      // 高级用法数据
      advancedVisible: false,
      advancedConfig: {} as UTSJSONObject,
      
      // 测试数据
      testResults: [] as string[]
    }
  },
  
  methods: {
    // 基础功能测试
    testBasicFeatures(): void {
      this.basicVisible = true
      this.addTestResult('基础功能测试通过')
    },
    
    // 边界情况测试
    testEdgeCases(): void {
      // 空数据测试
      this.basicData = []
      
      // 大量数据测试
      this.basicData = this.generateLargeData()
      
      this.addTestResult('边界情况测试完成')
    },
    
    addTestResult(result: string): void {
      this.testResults.push(result)
    }
  }
}
```

### 2. 功能测试检查清单
```uts
// 组件测试检查清单
const COMPONENT_TEST_CHECKLIST = [
  // 基础功能
  '✓ 组件正常渲染',
  '✓ Props 正确传递',
  '✓ 事件正确触发',
  '✓ 样式正确显示',
  
  // 交互功能
  '✓ 点击事件响应',
  '✓ 状态变更正确',
  '✓ 动画效果流畅',
  
  // 边界情况
  '✓ 空数据处理',
  '✓ 异常数据处理',
  '✓ 大量数据渲染',
  '✓ 快速操作响应',
  
  // 平台兼容
  '✓ Android 平台测试',
  '✓ iOS 平台测试',
  '✓ 不同屏幕尺寸',
  
  // 性能测试
  '✓ 内存使用正常',
  '✓ 渲染性能良好',
  '✓ 无内存泄漏'
]
```

## 📚 组件文档规范

### 1. 组件 README 结构
```markdown
# FlyerActionsheet 操作面板

从底部弹出的操作面板组件，用于展示一组操作选项。

## 基本用法

\`\`\`vue
<flyer-actionsheet
  :visible="visible"
  :set-visible="setVisible"
  :item-list="itemList"
  tips="请选择操作"
  @click="handleClick"
/>
\`\`\`

## Props

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| visible | Boolean | false | 是否显示 |
| setVisible | Function | - | 状态控制函数 |
| itemList | UTSJSONObject[] | [] | 操作项列表 |
| tips | String | '' | 提示信息 |

## Events

| 事件名 | 参数 | 说明 |
|--------|------|------|
| click | { index, item } | 点击操作项 |
| cancel | - | 点击取消 |

## 样式变量

| 变量名 | 默认值 | 说明 |
|--------|--------|------|
| --actionsheet-bg | #F8F8F8 | 背景色 |
| --actionsheet-radius | 24rpx | 圆角 |
```

### 2. Props 文档规范
```uts
// 在组件代码中添加详细注释
props: {
  /**
   * 是否显示操作面板
   * @type Boolean
   * @required true
   */
  visible: {
    type: Boolean,
    required: true
  },
  
  /**
   * 操作项列表
   * @type UTSJSONObject[]
   * @description 每个项目包含 text, value, disabled 等字段
   * @example [{ text: '删除', value: 'delete', color: '#FF0000' }]
   */
  itemList: {
    type: Array as PropType<UTSJSONObject[]>,
    required: true
  },
  
  /**
   * 提示信息
   * @type String
   * @default ''
   * @description 显示在操作项上方的提示文字
   */
  tips: {
    type: String,
    default: ''
  }
}
```

## 🚀 性能优化指南

### 1. 渲染性能优化
```uts
// ✅ 使用计算属性缓存复杂计算
computed: {
  processedList(): UTSJSONObject[] {
    // 复杂的数据处理逻辑
    return this.itemList.map((item: UTSJSONObject) => {
      return {
        ...item,
        displayText: this.formatText(item['text'] as string)
      }
    })
  }
},

// ✅ 避免在模板中进行复杂计算
// ❌ 错误
template: `<text>{{ formatComplexText(item.text, item.type, index) }}</text>`

// ✅ 正确
template: `<text>{{ item.displayText }}</text>`
```

### 2. 内存管理
```uts
// ✅ 正确的清理逻辑
methods: {
  cleanup(): void {
    // 清理定时器
    if (this.timer != null) {
      clearTimeout(this.timer)
      this.timer = null
    }
    
    // 清理事件监听
    if (this.listener != null) {
      this.removeEventListener()
      this.listener = null
    }
  }
},

// 组件销毁时清理
beforeUnmount(): void {
  this.cleanup()
}
```

## 🔍 调试与问题排查

### 1. 常见问题诊断
```uts
// 调试模式开关
data() {
  return {
    debugMode: false, // 生产环境设为 false
    debugInfo: {} as UTSJSONObject
  }
},

methods: {
  debug(message: string, data?: any): void {
    if (this.debugMode) {
      console.log(`[${this.$options.name}] ${message}`, data)
      this.debugInfo[message] = {
        timestamp: Date.now(),
        data: data
      }
    }
  },
  
  handleClick(index: number): void {
    this.debug('点击事件', { index: index, item: this.itemList[index] })
    // ... 业务逻辑
  }
}
```

### 2. 性能监控
```uts
methods: {
  performanceTest(): void {
    const startTime = Date.now()
    
    // 执行操作
    this.heavyOperation()
    
    const endTime = Date.now()
    const duration = endTime - startTime
    
    console.log(`操作耗时: ${duration}ms`)
    
    if (duration > 100) {
      console.warn('性能警告: 操作耗时过长')
    }
  }
}
```

## 📋 组件开发检查清单

### 开发阶段
- [ ] 组件命名符合规范 (Flyer + 功能名)
- [ ] Props 类型定义正确 (UTSJSONObject[] 用于复杂数组)
- [ ] 事件命名和数据结构规范
- [ ] CSS 类命名遵循 BEM 规范
- [ ] 内联样式使用字符串拼接
- [ ] 文字内容使用 text 标签包裹
- [ ] 响应式单位使用 rpx

### 测试阶段
- [ ] 基础功能测试通过
- [ ] 边界情况处理正确
- [ ] 错误状态显示合理
- [ ] 性能表现良好
- [ ] 内存无泄漏
- [ ] 多平台兼容性测试

### 发布阶段
- [ ] 组件文档完整
- [ ] 示例代码清晰
- [ ] API 接口稳定
- [ ] 版本号规范
- [ ] 变更日志记录

## 💡 最佳实践总结

1. **组件设计**: 单一职责、高内聚、低耦合
2. **Props 设计**: 类型安全、默认值合理、命名清晰
3. **事件设计**: 数据完整、命名统一、时机准确
4. **样式设计**: 响应式优先、性能考虑、维护性好
5. **测试策略**: 功能全面、边界覆盖、性能监控
6. **文档维护**: 及时更新、示例丰富、说明清晰

遵循这些规范，可以确保组件的质量、性能和可维护性，为用户提供一致的开发体验。
