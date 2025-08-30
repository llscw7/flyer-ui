# Flyer UI - Copilot Instructions

This is **Flyer UI**, a mobile UI component library built with uni-app x framework using UTS language (strict TypeScript variant).

## 🏗️ Architecture Essentials

**Component Structure**: `components/flyer-[name]/flyer-[name].uvue`
- Auto-registered via easycom (no imports needed)
- Use Vue 3 Composition API with `<script setup lang="uts">`
- Support APP-Android & APP-iOS with conditional compilation

**Key Files**:
- `pages.json` - easycom config + route definitions
- `common/flyer-ui.scss` - design system variables
- `components/` - component library source

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

// ✅ ALWAYS use UTS patterns:
const props = defineProps<{ type?: string }>() // Generic props
let data: UTSJSONObject = {}                   // UTSJSONObject type
if (value != null) { }                         // Explicit conditions
let value: string | null = null                // null instead of undefined
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

## 📚 Key Reference Files

- `common/flyer-ui.scss` - Design tokens ($flyer-color-primary, $flyer-button-normal-height)
- `pages.json` - easycom config enables auto-registration
- `components/flyer-button/flyer-button.uvue` - Reference implementation

## ✅ Development Checklist

When building components or pages:
- [ ] Use `<script setup lang="uts">` with Vue 3 Composition API
- [ ] Props defined with `defineProps<{}>()` TypeScript generics
- [ ] Events defined with `defineEmits<{}>()`
- [ ] Text content wrapped in `<text>` or `<button>` components
- [ ] Use explicit boolean conditions (`value != null`, not `if (value)`)
- [ ] Use `UTSJSONObject` instead of `object` type
- [ ] Add conditional compilation for scrollable pages (`#ifdef APP`)
- [ ] Components follow `flyer-[name]` naming convention
- [ ] Leverage design tokens from `common/flyer-ui.scss`

