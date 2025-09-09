---
mode: agent
---
# uni-app x 测试开发指南

基于 Flyer UI 项目的测试策略和质量保证规范，涵盖组件测试、集成测试和用户体验测试。

## 🧪 测试架构概览

### 1. 测试层级结构
```
测试金字塔
├── E2E 测试 (5%)     - 端到端用户流程测试
├── 集成测试 (15%)    - 组件间交互测试  
├── 单元测试 (80%)    - 组件功能测试
└── 静态分析         - 代码质量检查
```

### 2. 测试文件组织
```
components/
├── flyer-actionsheet/
│   ├── flyer-actionsheet.uvue
│   ├── __tests__/
│   │   ├── flyer-actionsheet.spec.ts
│   │   ├── actionsheet.integration.spec.ts
│   │   └── actionsheet.e2e.spec.ts
│   └── README.md
├── flyer-button/
│   ├── flyer-button.uvue
│   └── __tests__/
│       └── flyer-button.spec.ts
└── ...

tests/
├── unit/              # 单元测试
├── integration/       # 集成测试
├── e2e/              # 端到端测试
├── fixtures/         # 测试数据
├── helpers/          # 测试工具
└── setup/            # 测试配置
```

## 🔬 单元测试规范

### 1. 组件测试基础
```typescript
// flyer-actionsheet.spec.ts
import { mount, shallowMount } from '@vue/test-utils'
import FlyerActionsheet from '../flyer-actionsheet.uvue'

describe('FlyerActionsheet', () => {
  // 基础渲染测试
  it('should render correctly', () => {
    const wrapper = shallowMount(FlyerActionsheet, {
      props: {
        show: false,
        itemList: []
      }
    })
    
    expect(wrapper.exists()).toBe(true)
    expect(wrapper.classes()).toContain('flyer-actionsheet')
  })
  
  // Props 测试
  describe('Props', () => {
    it('should display title when provided', () => {
      const title = '请选择操作'
      const wrapper = mount(FlyerActionsheet, {
        props: {
          show: true,
          title,
          itemList: []
        }
      })
      
      const titleElement = wrapper.find('.flyer-actionsheet__title')
      expect(titleElement.exists()).toBe(true)
      expect(titleElement.text()).toBe(title)
    })
    
    it('should render item list correctly', () => {
      const itemList = [
        { text: '拍照', color: '#007AFF' },
        { text: '从相册选择', color: '#007AFF' },
        { text: '删除', color: '#FF3B30' }
      ]
      
      const wrapper = mount(FlyerActionsheet, {
        props: {
          show: true,
          itemList
        }
      })
      
      const items = wrapper.findAll('.flyer-actionsheet__btn')
      expect(items).toHaveLength(itemList.length)
      
      items.forEach((item, index) => {
        expect(item.text()).toBe(itemList[index].text)
      })
    })
  })
  
  // 事件测试
  describe('Events', () => {
    it('should emit click event when item is clicked', async () => {
      const itemList = [
        { text: '选项1' },
        { text: '选项2' }
      ]
      
      const wrapper = mount(FlyerActionsheet, {
        props: {
          show: true,
          itemList
        }
      })
      
      const firstItem = wrapper.find('.flyer-actionsheet__btn')
      await firstItem.trigger('click')
      
      expect(wrapper.emitted('click')).toBeTruthy()
      expect(wrapper.emitted('click')?.[0]).toEqual([{
        index: 0,
        item: itemList[0]
      }])
    })
    
    it('should emit cancel event when cancel button is clicked', async () => {
      const wrapper = mount(FlyerActionsheet, {
        props: {
          show: true,
          itemList: [{ text: '选项' }]
        }
      })
      
      const cancelButton = wrapper.find('.flyer-actionsheet__cancel')
      await cancelButton.trigger('click')
      
      expect(wrapper.emitted('cancel')).toBeTruthy()
    })
    
    it('should emit close event when mask is clicked', async () => {
      const wrapper = mount(FlyerActionsheet, {
        props: {
          show: true,
          itemList: [],
          maskClosable: true
        }
      })
      
      const mask = wrapper.find('.flyer-actionsheet__mask')
      await mask.trigger('click')
      
      expect(wrapper.emitted('close')).toBeTruthy()
    })
  })
  
  // 状态测试
  describe('States', () => {
    it('should show/hide correctly based on show prop', async () => {
      const wrapper = mount(FlyerActionsheet, {
        props: {
          show: false,
          itemList: []
        }
      })
      
      expect(wrapper.find('.flyer-actionsheet__mask').classes()).not.toContain('flyer-actionsheet__mask--show')
      
      await wrapper.setProps({ show: true })
      expect(wrapper.find('.flyer-actionsheet__mask').classes()).toContain('flyer-actionsheet__mask--show')
    })
    
    it('should handle disabled items correctly', () => {
      const itemList = [
        { text: '正常选项' },
        { text: '禁用选项', disabled: true }
      ]
      
      const wrapper = mount(FlyerActionsheet, {
        props: {
          show: true,
          itemList
        }
      })
      
      const items = wrapper.findAll('.flyer-actionsheet__btn')
      expect(items[0].classes()).not.toContain('flyer-actionsheet__btn--disabled')
      expect(items[1].classes()).toContain('flyer-actionsheet__btn--disabled')
    })
  })
})
```

### 2. 工具函数测试
```typescript
// store.spec.ts
import { setVisible, isVisible } from '../store.uts'

describe('ActionSheet Store', () => {
  beforeEach(() => {
    // 重置状态
    setVisible(false)
  })
  
  it('should manage visibility state correctly', () => {
    expect(isVisible()).toBe(false)
    
    setVisible(true)
    expect(isVisible()).toBe(true)
    
    setVisible(false)
    expect(isVisible()).toBe(false)
  })
  
  it('should handle multiple actionsheets independently', () => {
    // 测试多个实例的状态管理
    const key1 = 'actionsheet-1'
    const key2 = 'actionsheet-2'
    
    setVisible(true, key1)
    setVisible(false, key2)
    
    expect(isVisible(key1)).toBe(true)
    expect(isVisible(key2)).toBe(false)
  })
})
```

## 🔗 集成测试规范

### 1. 组件交互测试
```typescript
// actionsheet.integration.spec.ts
import { mount } from '@vue/test-utils'
import ActionsheetExample from '../../../pages/examples/actionsheet/actionsheet.uvue'

describe('Actionsheet Integration', () => {
  it('should integrate with page correctly', async () => {
    const wrapper = mount(ActionsheetExample)
    
    // 测试页面初始状态
    expect(wrapper.find('.flyer-actionsheet').exists()).toBe(true)
    expect(wrapper.vm.show).toBe(false)
    
    // 触发显示
    const showButton = wrapper.find('[data-testid="show-actionsheet"]')
    await showButton.trigger('click')
    
    expect(wrapper.vm.show).toBe(true)
    expect(wrapper.find('.flyer-actionsheet__mask--show').exists()).toBe(true)
  })
  
  it('should handle complex interactions', async () => {
    const wrapper = mount(ActionsheetExample)
    
    // 显示 actionsheet
    await wrapper.setData({ show: true })
    
    // 选择一个选项
    const firstOption = wrapper.find('.flyer-actionsheet__btn')
    await firstOption.trigger('click')
    
    // 验证选择结果
    expect(wrapper.vm.selectedIndex).toBe(0)
    expect(wrapper.vm.show).toBe(false)
  })
})
```

### 2. 多组件协作测试
```typescript
// popup-actionsheet.integration.spec.ts
describe('Popup with Actionsheet Integration', () => {
  it('should handle nested popups correctly', async () => {
    const wrapper = mount(ComplexPopupExample)
    
    // 打开第一层弹窗
    await wrapper.find('[data-testid="open-popup"]').trigger('click')
    expect(wrapper.find('.flyer-popup').classes()).toContain('flyer-popup--show')
    
    // 在弹窗内打开 actionsheet
    await wrapper.find('[data-testid="open-actionsheet"]').trigger('click')
    expect(wrapper.find('.flyer-actionsheet').classes()).toContain('flyer-actionsheet--show')
    
    // 测试层级关系
    const popup = wrapper.find('.flyer-popup')
    const actionsheet = wrapper.find('.flyer-actionsheet')
    
    expect(getComputedStyle(popup.element).zIndex).toBeLessThan(
      getComputedStyle(actionsheet.element).zIndex
    )
  })
})
```

## 🚀 端到端测试规范

### 1. 用户流程测试
```typescript
// actionsheet.e2e.spec.ts
import { test, expect } from '@playwright/test'

test.describe('Actionsheet E2E', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('/pages/examples/actionsheet/actionsheet')
  })
  
  test('should complete full user interaction flow', async ({ page }) => {
    // 页面加载验证
    await expect(page.locator('.content')).toBeVisible()
    
    // 打开 actionsheet
    await page.click('[data-testid="show-basic-actionsheet"]')
    await expect(page.locator('.flyer-actionsheet__mask')).toBeVisible()
    
    // 选择一个选项
    await page.click('.flyer-actionsheet__btn:first-child')
    
    // 验证结果
    await expect(page.locator('[data-testid="selected-result"]')).toContainText('选择了: 拍照')
    await expect(page.locator('.flyer-actionsheet__mask')).not.toBeVisible()
  })
  
  test('should handle cancel action correctly', async ({ page }) => {
    await page.click('[data-testid="show-basic-actionsheet"]')
    await page.click('.flyer-actionsheet__cancel')
    
    await expect(page.locator('.flyer-actionsheet__mask')).not.toBeVisible()
    await expect(page.locator('[data-testid="action-result"]')).toContainText('取消操作')
  })
  
  test('should support mask click to close', async ({ page }) => {
    await page.click('[data-testid="show-mask-closable"]')
    
    // 点击遮罩区域
    await page.click('.flyer-actionsheet__mask', { position: { x: 50, y: 50 } })
    
    await expect(page.locator('.flyer-actionsheet__mask')).not.toBeVisible()
  })
})
```

### 2. 跨平台测试
```typescript
// platform.e2e.spec.ts
test.describe('Cross-platform Tests', () => {
  ['ios', 'android'].forEach(platform => {
    test(`should work correctly on ${platform}`, async ({ page }) => {
      // 设置平台特定的环境
      await page.addInitScript((platform) => {
        window.uni = { getSystemInfoSync: () => ({ platform }) }
      }, platform)
      
      await page.goto('/pages/examples/actionsheet/actionsheet')
      
      // 验证平台特定的行为
      await page.click('[data-testid="show-actionsheet"]')
      
      const actionsheet = page.locator('.flyer-actionsheet__wrap')
      if (platform === 'ios') {
        await expect(actionsheet).toHaveCSS('border-radius', '24rpx 24rpx 0 0')
      }
      
      // 验证安全区域适配
      if (platform === 'ios') {
        await expect(page.locator('.flyer-actionsheet__safe-bottom')).toBeVisible()
      }
    })
  })
})
```

## 📊 性能测试规范

### 1. 渲染性能测试
```typescript
// performance.spec.ts
describe('Performance Tests', () => {
  it('should render large lists efficiently', async () => {
    const largeItemList = Array.from({ length: 100 }, (_, i) => ({
      text: `选项 ${i + 1}`,
      value: i
    }))
    
    const startTime = performance.now()
    
    const wrapper = mount(FlyerActionsheet, {
      props: {
        show: true,
        itemList: largeItemList
      }
    })
    
    const endTime = performance.now()
    const renderTime = endTime - startTime
    
    expect(renderTime).toBeLessThan(100) // 100ms 内完成渲染
    expect(wrapper.findAll('.flyer-actionsheet__btn')).toHaveLength(100)
  })
  
  it('should handle frequent show/hide operations', async () => {
    const wrapper = mount(FlyerActionsheet, {
      props: {
        show: false,
        itemList: [{ text: '选项' }]
      }
    })
    
    const startTime = performance.now()
    
    // 快速切换显示/隐藏 100 次
    for (let i = 0; i < 100; i++) {
      await wrapper.setProps({ show: true })
      await wrapper.setProps({ show: false })
    }
    
    const endTime = performance.now()
    const operationTime = endTime - startTime
    
    expect(operationTime).toBeLessThan(1000) // 1 秒内完成
  })
})
```

### 2. 内存泄漏测试
```typescript
// memory.spec.ts
describe('Memory Tests', () => {
  it('should clean up event listeners on unmount', () => {
    const addEventListenerSpy = jest.spyOn(document, 'addEventListener')
    const removeEventListenerSpy = jest.spyOn(document, 'removeEventListener')
    
    const wrapper = mount(FlyerActionsheet, {
      props: {
        show: true,
        itemList: []
      }
    })
    
    // 验证事件监听器被添加
    expect(addEventListenerSpy).toHaveBeenCalled()
    
    wrapper.unmount()
    
    // 验证事件监听器被移除
    expect(removeEventListenerSpy).toHaveBeenCalled()
    expect(removeEventListenerSpy).toHaveBeenCalledTimes(addEventListenerSpy.mock.calls.length)
  })
})
```

## 🛠️ 测试工具与配置

### 1. 测试环境配置
```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  test: {
    environment: 'jsdom',
    globals: true,
    setupFiles: ['./tests/setup/global.ts'],
    coverage: {
      provider: 'v8',
      reporter: ['text', 'html', 'lcov'],
      exclude: [
        'node_modules/',
        'tests/',
        '**/*.spec.ts',
        '**/*.test.ts'
      ]
    }
  },
  resolve: {
    alias: {
      '@': '/src',
      '@components': '/components'
    }
  }
})
```

### 2. 测试工具函数
```typescript
// tests/helpers/test-utils.ts
import { mount, VueWrapper } from '@vue/test-utils'
import { Component } from 'vue'

// 创建测试包装器
export function createWrapper<T extends Component>(
  component: T,
  options: any = {}
) {
  return mount(component, {
    global: {
      stubs: {
        // 模拟全局组件
      }
    },
    ...options
  })
}

// 等待异步操作
export async function waitFor(callback: () => boolean, timeout = 1000) {
  const startTime = Date.now()
  
  while (!callback() && Date.now() - startTime < timeout) {
    await new Promise(resolve => setTimeout(resolve, 10))
  }
  
  if (!callback()) {
    throw new Error(`Timeout waiting for condition after ${timeout}ms`)
  }
}

// 模拟触摸事件
export function mockTouchEvent(type: string, touches: Array<{ clientX: number; clientY: number }>) {
  return new TouchEvent(type, {
    touches: touches.map(touch => new Touch({
      identifier: Date.now(),
      target: document.body,
      ...touch
    }))
  })
}

// 模拟动画完成
export function mockAnimationEnd(element: Element) {
  element.dispatchEvent(new AnimationEvent('animationend'))
  element.dispatchEvent(new TransitionEvent('transitionend'))
}
```

### 3. 数据模拟
```typescript
// tests/fixtures/actionsheet.ts
export const mockActionsheetData = {
  basic: {
    itemList: [
      { text: '拍照', color: '#007AFF' },
      { text: '从相册选择', color: '#007AFF' },
      { text: '删除', color: '#FF3B30' }
    ]
  },
  
  withTitle: {
    title: '请选择操作',
    itemList: [
      { text: '编辑' },
      { text: '分享' },
      { text: '收藏' }
    ]
  },
  
  withDisabled: {
    itemList: [
      { text: '可用选项' },
      { text: '禁用选项', disabled: true },
      { text: '另一个选项' }
    ]
  },
  
  large: {
    itemList: Array.from({ length: 50 }, (_, i) => ({
      text: `选项 ${i + 1}`,
      value: i
    }))
  }
}

// 模拟用户交互
export const mockUserInteractions = {
  clickItem: (wrapper: VueWrapper<any>, index: number) => {
    return wrapper.findAll('.flyer-actionsheet__btn')[index].trigger('click')
  },
  
  clickCancel: (wrapper: VueWrapper<any>) => {
    return wrapper.find('.flyer-actionsheet__cancel').trigger('click')
  },
  
  clickMask: (wrapper: VueWrapper<any>) => {
    return wrapper.find('.flyer-actionsheet__mask').trigger('click')
  }
}
```

## 📋 测试检查清单

### 单元测试
- [ ] 组件正确渲染
- [ ] Props 正确传递和响应
- [ ] 事件正确触发和传递
- [ ] 计算属性正确计算
- [ ] 方法正确执行
- [ ] 状态正确管理
- [ ] 边界条件处理

### 集成测试
- [ ] 组件间正确交互
- [ ] 页面级功能正常
- [ ] 路由跳转正确
- [ ] 数据流正确
- [ ] 状态同步正确

### 端到端测试
- [ ] 完整用户流程
- [ ] 跨平台兼容性
- [ ] 响应式布局
- [ ] 性能表现
- [ ] 错误处理
- [ ] 无障碍访问

### 质量指标
- [ ] 测试覆盖率 > 80%
- [ ] 所有测试通过
- [ ] 性能测试达标
- [ ] 无内存泄漏
- [ ] 代码质量检查通过

## 💡 测试最佳实践

1. **测试驱动开发 (TDD)**：先写测试，再实现功能
2. **单一职责**：每个测试只验证一个功能点
3. **清晰命名**：测试名称清楚描述测试内容
4. **数据隔离**：每个测试使用独立的测试数据
5. **模拟依赖**：合理使用 Mock 隔离外部依赖
6. **持续集成**：自动化运行测试，确保代码质量
7. **定期维护**：及时更新测试用例，保持测试有效性

遵循这些测试规范，可以确保组件的质量、稳定性和可维护性。
