# UTS 快速参考卡片

## 🎯 组合式 API 语法 (必须使用)

```vue
<script setup lang="uts">
// 导入 Vue APIs
import { ref, reactive, computed, watch, onMounted } from 'vue'

// 组件选项
defineOptions({
  name: 'FlyerComponent'
})

// Props 定义
const props = defineProps<{
  type?: string,
  disabled?: boolean
}>()

// 事件定义
const emit = defineEmits<{
  click: [event: any]
}>()

// 响应式数据
const loading = ref(false)
const formData = reactive({
  name: '',
  age: 0
})

// 计算属性
const classes = computed(() => {
  return `btn--${props.type || 'default'}`
})

// 方法
function handleClick(event: any) {
  emit('click', event)
}

// 生命周期
onMounted(() => {
  console.log('mounted')
})
</script>
```

## 🚫 避免使用的TS语法

```uts
// ❌ TypeScript联合类型导出
export type ButtonType = 'primary' | 'success'

// ❌ PropType泛型
type: String as PropType<ButtonType>

// ❌ interface定义对象类型  
interface Props { name: string }

// ❌ object类型
let data: object = {}

// ❌ 隐式类型转换条件
if (value) { }
if (!array.length) { }

// ❌ undefined
let value: string | undefined

// ❌ var声明
var name = 'test'

// ❌ 三重等号
if (value === null) { }
```

## ✅ 正确的UTS语法

```uts
// ✅ 组合式 API Props 定义
const props = defineProps<{
  // 按钮类型：primary | success | warning
  type?: string,
  disabled?: boolean
}>()

// ✅ 使用type定义对象类型
type ButtonProps = {
  text: string,
  disabled: boolean
}

// ✅ UTSJSONObject替代object
let data: UTSJSONObject = {}

// ✅ 响应式数据
const loading = ref(false)
const form = reactive({
  name: '',
  email: ''
})

// ✅ 事件处理方法
function handleClick(event: any) {
  if (props.disabled) {
    return
  }
  emit('click', event)
}
```

// ✅ 明确的布尔条件
if (value != null) { }
if (array.length > 0) { }

// ✅ 空值安全处理
let value: string | null = null
value?.toUpperCase()

// ✅ let/const声明
let name = 'test'
const config = { type: 'primary' }

// ✅ 双等号比较
if (value == null) { }
```

## 🎯 核心规则速记

1. **强类型**：不能隐式转换类型
2. **条件语句**：必须是布尔类型条件
3. **空值安全**：使用 `| null` 和 `?.` 安全调用
4. **对象类型**：用 `type` 不用 `interface`
5. **变量声明**：用 `let/const` 不用 `var`
6. **比较运算**：用 `==/!=` 不用 `===/!==`
7. **样式规范**：只用类选择器，只用flex布局
8. **组件命名**：flyer-前缀，easycom自动注册
