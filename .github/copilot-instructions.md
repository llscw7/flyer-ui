# Flyer UI - Copilot Instructions

This is **Flyer UI**, a mobile UI component library built with uni-app x framework using UTS language (strict TypeScript variant).

## 🏗️ Architecture Essentials

**Component Structure**: `components/flyer-[name]/flyer-[name].uvue`
- Auto-registered via easycom (no imports needed)
- Use Vue 3 Composition API with `<script setup lang="uts">`
- Support APP-Android & APP-iOS with conditional compilation
- Global state components use store pattern with `store.uts`

**Key Files**:
- `pages.json` - easycom config + route definitions
- `common/flyer-ui.scss` - design system variables
- `components/` - component library source
- `components/*/store.uts` - global state management for modals/overlays

## 🚀 Component Development Pattern

**Required Template** (Vue 3 Composition API):
```vue
<template>
  <view class="flyer-[name]" :class="computedClasses" @click="handleClick">
    <text>{{ text }}</text>
  </view>
</template>

<script setup lang="uts">
import { ref, computed } from 'vue'

defineOptions({ name: 'Flyer[Name]' })

const props = defineProps<{
  type?: string,
  disabled?: boolean
}>()

const emit = defineEmits<{ 
  click: [event: any] 
}>()

const isLoading = ref(false)
const computedClasses = computed(() => `flyer-[name]--${props.type}`)

function handleClick(event: any) {
  if (props.disabled) return
  emit('click', event)
}
</script>
```

## ⚡ UTS Critical Differences from TypeScript

```uts
// ❌ NEVER use these TypeScript patterns:
export type ButtonType = 'primary' | 'success'  // Union types
let data: object = {}                           // object type  
if (value) { }                                  // Implicit conversion
let value: string | undefined                   // undefined type
export const state: State = {}                 // Non-reactive state
mutations.reset()                              // Object method self-reference
const { reset } = mutations                    // Object destructuring

// ✅ ALWAYS use UTS patterns:
const props = defineProps<{ type?: string }>() // Generic props
let data: UTSJSONObject = {}                   // UTSJSONObject type
if (value != null) { }                         // Explicit conditions
let value: string | null = null                // null instead of undefined
export const state = reactive({} as State)     // Reactive state with reactive()
resetState()                                   // Internal helper functions
state.resolvePromise?.(value)                  // Safe navigation operators
```

## 🚨 UTS Compilation Error Prevention

**Common Issues & Solutions:**

```uts
// ❌ Circular reference errors:
export const mutations = {
  reset() {
    mutations.reset() // Creates circular dependency
  }
}

// ✅ Use internal helper functions:
function resetState() {
  state.property = null
}
export const mutations = {
  reset() {
    resetState() // Clean separation
  }
}

// ❌ Unsafe property access:
state.resolvePromise(value)  // May crash if null

// ✅ Safe navigation:
state.resolvePromise?.(value) // Safe optional call
if (state.resolvePromise != null) {
  state.resolvePromise(value) // Explicit null check
}

// ❌ Implicit type conversion:
if (config.maskClosable) { } // Boolean may be undefined

// ✅ Explicit null/type checks:
if (config.maskClosable != null ? config.maskClosable : true) { }
config?.maskClosable ?? true // Safe defaults with nullish coalescing
```

## 🎨 ucss Styling Rules

**Critical**: Text MUST be in `<text>` or `<button>` components. Only flex layout allowed.

```vue
<template>
  <!-- ✅ Correct -->
  <view class="container">
    <text class="title">Hello</text>
  </view>
  
  <!-- ❌ Wrong -->
  <view class="title">Hello</view>
</template>

<style>
.container {
  flex-direction: column; /* Default is column, not row */
}
</style>
```

## 📱 Platform-Specific Patterns

**Scrollable content** needs conditional compilation:
```vue
<template>
  <!-- #ifdef APP -->
  <scroll-view class="container">
  <!-- #endif -->
    <view class="content">
      <!-- Your content -->
    </view>
  <!-- #ifdef APP -->
  </scroll-view>
  <!-- #endif -->
</template>
```

**Platform-specific code**:
```uts
// #ifdef APP-ANDROID
// Android-only logic
// #endif

// #ifdef APP-IOS  
// iOS-only logic
// #endif
```

## 🔧 Development Workflow

**Auto-registration** (no imports needed):
```vue
<!-- Works automatically via easycom -->
<flyer-button type="primary" text="Click me" @click="handleClick" />
```

**Component usage patterns** from existing components:
- `flyer-button`: `type="primary|success|warning|danger"`, `size="large|normal|small"`
- `flyer-icon`: `name="uni-icon-name"` or `font-family="iconfont"`  
- `flyer-dialog`: Global store management with `mutations.show()`, `mutations.alert()`, `mutations.showConfirm()`

## 🏪 Store Component Pattern

**For components with global state** (modals, overlays, notifications):

```uts
// store.uts
import { reactive } from 'vue'

export type State = {
  visible: boolean
  title: string
  content: string
  targetId: string | null
  zIndex: number
  resolvePromise: ((value: any) => void) | null
  rejectPromise: ((reason?: any) => void) | null
}

export const state = reactive({
  visible: false,
  title: '提示',
  content: '',
  targetId: null,
  zIndex: 9999,
  resolvePromise: null,
  rejectPromise: null
} as State)

export const mutations = {
  show(config?: { id?: string, title?: string, content?: string }): Promise<any> {
    // Update state
    state.visible = true
    state.title = config?.title || '提示'
    state.content = config?.content || ''
    state.targetId = config?.id || null
    
    return new Promise<any>((resolve, reject) => {
      state.resolvePromise = resolve
      state.rejectPromise = reject
    })
  },
  
  confirm(value?: any) {
    state.visible = false
    const resolve = state.resolvePromise
    resetState() // Use internal helper function
    if (resolve != null) {
      resolve(value || true)
    }
  },
  
  cancel(value?: any) {
    state.visible = false
    const resolve = state.resolvePromise  
    resetState() // Use internal helper function
    if (resolve != null) {
      resolve(value || false)
    }
  },
  
  close() {
    state.visible = false
    const reject = state.rejectPromise
    resetState() // Use internal helper function
    if (reject != null) {
      reject('closed')
    }
  },
  
  reset() {
    resetState() // Delegate to internal function
  }
}

// Internal helper function to avoid circular references
function resetState() {
  state.targetId = null
  state.resolvePromise = null
  state.rejectPromise = null
}
```

**Component template**:
```vue
<script setup lang="uts">
import { computed } from 'vue'
import { state, mutations } from './store.uts'

const props = defineProps<{ id?: string }>()

const shouldShow = computed(() => {
  return state.targetId == null ? 
    state.visible : 
    state.visible && state.targetId == props.id
})

function handleConfirm() {
  mutations.confirm()
}

function handleCancel() {
  mutations.cancel()
}
</script>
```

**Usage in pages**:
```vue
<template>
  <flyer-dialog />
  <!-- Multi-instance support -->
  <flyer-dialog id="main" />
  <flyer-dialog id="secondary" />
</template>

<script setup lang="uts">
import { mutations } from '@/components/flyer-dialog/store.uts'

async function showAlert() {
  await mutations.alert('Alert message', 'Title')
}

async function showConfirm() {
  const result = await mutations.showConfirm('Confirm message?')
  if (result) {
    console.log('User confirmed')
  }
}

async function showMultiInstance() {
  // Target specific instance
  await mutations.show({
    id: 'main',
    title: 'Main Dialog',
    content: 'Main content'
  })
}
</script>
```

## 📚 Key Reference Files

- `common/flyer-ui.scss` - Design tokens ($flyer-color-primary, $flyer-button-normal-height)
- `pages.json` - easycom config enables auto-registration
- `components/flyer-button/flyer-button.uvue` - Basic component implementation
- `components/flyer-dialog/` - Store component complete reference
- `.github/STORE_COMPONENT_GUIDE.md` - Comprehensive store development guide

## ✅ Development Checklist

When building components or pages:
- [ ] Use `<script setup lang="uts">` with Vue 3 Composition API
- [ ] Props defined with `defineProps<{}>()` TypeScript generics
- [ ] Events defined with `defineEmits<{}>()`
- [ ] Text content wrapped in `<text>` or `<button>` components
- [ ] Use explicit boolean conditions (`value != null`, not `if (value)`)
- [ ] Use `UTSJSONObject` instead of `object` type
- [ ] Use `reactive()` for creating responsive state (not plain objects)
- [ ] Add conditional compilation for scrollable pages (`#ifdef APP`)
- [ ] Components follow `flyer-[name]` naming convention
- [ ] Leverage design tokens from `common/flyer-ui.scss`
- [ ] For store components: implement Promise-based async pattern
- [ ] For store components: support multi-instance with targetId pattern
- [ ] For store components: use internal resetState() function to avoid circular references
- [ ] For store components: use safe navigation operators (`?.`) for Promise calls
- [ ] Use explicit null checks instead of implicit boolean conversion
- [ ] Use `==` and `!=` instead of `===` and `!==` for UTS compatibility

## 🔍 Store Component Development Notes

**Critical patterns from flyer-dialog implementation:**

1. **Reactive State**: Always use `reactive()` - non-reactive objects won't trigger UI updates
2. **Promise Handling**: Store resolve/reject functions in state, call resetState() after operations  
3. **Multi-instance**: Use `targetId` + component `id` props for instance-specific control
4. **State Cleanup**: Implement resetState() internal function to prevent memory leaks
5. **Type Safety**: Export State type and use proper TypeScript interfaces
6. **Circular Reference Avoidance**: Use internal helper functions instead of object method self-references

**Store Component Workflow:**
```
1. Create store.uts with reactive state
2. Implement mutations with Promise returns  
3. Add internal resetState() helper function
4. Add component with computed shouldShow
5. Test single and multi-instance usage
6. Document API and usage examples
```

**Critical UTS Store Patterns:**

```uts
// ❌ Avoid circular references in mutations:
export const mutations = {
  reset() {
    mutations.reset() // Circular dependency error
  }
}

// ✅ Use internal helper functions:
function resetState() {
  state.targetId = null
  state.resolvePromise = null  
  state.rejectPromise = null
}

export const mutations = {
  reset() {
    resetState() // Clean separation
  },
  
  confirm(value?: any) {
    state.visible = false
    const resolve = state.resolvePromise
    resetState() // Always reset after operation
    resolve?.(value) // Safe navigation for Promise calls
  }
}

// ✅ Safe property access patterns:
state.maskClosable = config?.maskClosable != null ? config.maskClosable : true
const shouldClose = state.maskClosable ?? true // Nullish coalescing
if (state.resolvePromise != null) { // Explicit null check
  state.resolvePromise(value)
}
```

## 🐛 Common UTS Compilation Errors & Solutions

**Error 1: Circular References**
```
Error: Cannot access 'mutations' before initialization
```
**Solution**: Use internal helper functions instead of object method self-references:
```uts
// ❌ Circular reference
export const mutations = {
  reset() { mutations.reset() }
}

// ✅ Internal helper function  
function resetState() { /* reset logic */ }
export const mutations = {
  reset() { resetState() }
}
```

**Error 2: Object Destructuring**
```
Error: Destructuring assignment is not supported
```
**Solution**: Access properties directly:
```uts
// ❌ Destructuring
const { reset, show } = mutations

// ✅ Direct access
mutations.reset()
mutations.show()
```

**Error 3: Unsafe Property Access**
```
Error: Property 'resolvePromise' may be null
```
**Solution**: Use safe navigation or explicit null checks:
```uts
// ❌ Direct access
state.resolvePromise(value)

// ✅ Safe navigation
state.resolvePromise?.(value)

// ✅ Explicit null check
if (state.resolvePromise != null) {
  state.resolvePromise(value)
}
```

**Error 4: Implicit Boolean Conversion**
```
Error: Type 'boolean | undefined' cannot be used in condition
```
**Solution**: Use explicit null checks and defaults:
```uts
// ❌ Implicit conversion
if (config.maskClosable) { }

// ✅ Explicit null check with default
if (config?.maskClosable ?? true) { }

// ✅ Ternary with null check
const closable = config?.maskClosable != null ? config.maskClosable : true
```

**Error 5: Non-reactive State**
```
Error: State changes not triggering UI updates
```
**Solution**: Always use reactive() for state objects:
```uts
// ❌ Plain object
export const state = {} as State

// ✅ Reactive object
export const state = reactive({} as State)
```

