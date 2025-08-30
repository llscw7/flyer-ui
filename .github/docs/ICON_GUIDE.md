# Flyer UI 字体图标使用指南

## 概述

Flyer UI 支持两种字体图标方案：
1. **内置 uni-icon** - uni-app x 内置字体图标
2. **自定义 iconfont** - 自定义字体文件

## 内置 uni-icon 使用

### 支持的图标列表

| 图标名称 | Unicode | 说明 |
|----------|---------|------|
| forward | \uE600 | 前进 |
| back | \uE601 | 返回 |
| share | \uE602 | 分享 |
| favorites | \uE604 | 收藏 |
| home | \uE605 | 首页 |
| more | \uE606 | 更多 |
| close | \uE650 | 关闭 |
| down | \uE661 | 下拉 |
| circle | \uEA01 | 圆圈 |
| info | \uEA03 | 信息 |
| info-circle | \uEA04 | 信息圆圈 |
| success | \uEA06 | 成功 |
| success-circle | \uEA07 | 成功圆圈 |
| success-no-circle | \uEA08 | 成功无圆圈 |
| cancel-circle | \uEA0B | 取消圆圈 |
| warn | \uEA0F | 警告 |
| clear | \uEA14 | 清除 |
| download | \uEA19 | 下载 |
| waiting | \uEA1E | 等待 |
| search | \uEA23 | 搜索 |

### 使用方法

#### 1. 直接在text组件中使用

```vue
<template>
  <text style="font-family: uni-icon; font-size: 24px; color: #007aff;">
    \uE605
  </text>
</template>
```

#### 2. 使用flyer-icon组件

```vue
<template>
  <!-- 通过名称使用 -->
  <flyer-icon name="home" font-family="uni-icon" :size="24" color="#007aff"></flyer-icon>
  
  <!-- 通过Unicode使用 -->
  <flyer-icon name="\uE605" font-family="uni-icon" :size="24" color="#007aff"></flyer-icon>
</template>
```

## 自定义 iconfont 使用

### 1. 准备字体文件

项目已内置 iconfont 字体文件，包含丰富的图标资源：

```
static/
└── iconfont/
    ├── iconfont.ttf    # 字体文件
    ├── iconfont.woff   # Web字体文件
    ├── iconfont.woff2  # Web字体文件
    ├── iconfont.json   # 图标映射文件
    └── iconfont.css    # 样式文件
```

### 2. 可用图标列表

#### 金融理财类
| 图标名称 | Unicode | 说明 |
|----------|---------|------|
| caipiao | \ue600 | 彩票 |
| huiyuanqia | \ue601 | 会员卡 |
| shuzirenminbi | \ue602 | 数字人民币 |
| bitebi | \ue603 | 比特币 |
| jijin | \ue610 | 基金 |
| yanglaojin | \ue611 | 养老金 |
| xinyongdaikuan | \ue614 | 信用贷款 |
| jiebei | \ue61e | 借呗 |
| qihuo | \ue78d | 期货 |
| dingqicunkuan | \ue78e | 定期存款 |
| huangjin | \ue78f | 黄金 |
| gongjijin | \ue79d | 公积金 |
| gupiao | \ue63d | 股票 |
| yingshou | \ue607 | 应收 |
| yingfu | \ue60a | 应付 |
| jiangjin | \ue608 | 奖金 |

#### 生活服务类
| 图标名称 | Unicode | 说明 |
|----------|---------|------|
| fanqia | \ue645 | 饭卡 |
| gas-station-fill | \ue84a | 加油站 |
| yibaoka | \uea0f | 医保卡 |
| zhufangzujin | \ue609 | 住房租金 |
| deposit | \ue790 | 退押金 |
| hospital | \ue666 | 医院 |
| restaurant | \ue667 | 餐厅 |
| home2 | \ue66e | 家庭 |
| beer | \ue665 | 啤酒 |
| phone | \ue670 | 电话 |
| movie | \ue673 | 电影 |
| pet-paw | \ue674 | 宠物 |

#### 常用功能类
| 图标名称 | Unicode | 说明 |
|----------|---------|------|
| cash | \ue647 | 现金 |
| coupon | \ue649 | 优惠券 |
| exchange | \ue64a | 兑换 |
| calendar | \ue64b | 日历 |
| piechart | \ue64c | 饼图 |
| vip | \ue64f | VIP |
| thumbup-line | \ue652 | 点赞线条 |
| thumbup-fill | \ue658 | 点赞填充 |
| thumbdown-line | \ue655 | 点踩线条 |
| thumbdown-fill | \ue65a | 点踩填充 |
| star-line | \ue65d | 星星线条 |
| star-s-line | \ue65b | 小星星线条 |
| search-line | \ue65c | 搜索线条 |
| shopping-cart | \ue65e | 购物车 |
| shopping-basket | \ue65f | 购物篮 |
| money-cny | \ue661 | 人民币 |
| gift | \ue662 | 礼品 |
| cash-briefcase | \ue664 | 现金公文包 |
| heart | \ue66f | 爱心 |
| heart2 | \ue672 | 爱心2 |
| run | \ue671 | 跑步 |
| share2 | \ue677 | 分享2 |
| plane | \ue678 | 飞机 |
| cherry | \ue67a | 樱桃 |
| gamepad | \ue67e | 游戏手柄 |

### 3. 使用自定义图标

```vue
<template>
  <!-- 通过图标名称使用 -->
  <flyer-icon name="caipiao" font-family="iconfont" :size="24" color="#ff6b35"></flyer-icon>
  <flyer-icon name="bitebi" font-family="iconfont" :size="24" color="#ff6b35"></flyer-icon>
  <flyer-icon name="jijin" font-family="iconfont" :size="24" color="#ff6b35"></flyer-icon>
  
  <!-- 通过Unicode使用 -->
  <flyer-icon name="\ue600" font-family="iconfont" :size="24" color="#ff6b35"></flyer-icon>
  <flyer-icon name="\ue603" font-family="iconfont" :size="24" color="#ff6b35"></flyer-icon>
  
  <!-- 直接在text组件中使用 -->
  <text style="font-family: iconfont; font-size: 24px; color: #ff6b35;">
    \ue600
  </text>
</template>
```

## flyer-icon 组件 API

### Props

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| name | String | '' | 图标名称或Unicode字符 |
| size | String/Number | 16 | 图标大小，支持数字(px)或字符串 |
| color | String | '#333333' | 图标颜色 |
| font-family | String | 'uni-icon' | 字体族：uni-icon / iconfont / 自定义 |
| clickable | Boolean | false | 是否可点击 |
| custom-class | String | '' | 自定义类名 |
| custom-style | Object | {} | 自定义样式 |

### Events

| 事件名 | 说明 | 参数 |
|--------|------|------|
| click | 点击事件（需要设置clickable为true） | event |

### 使用示例

```vue
<template>
  <!-- 基础用法 -->
  <flyer-icon name="home" :size="24" color="#007aff"></flyer-icon>
  
  <!-- 可点击 -->
  <flyer-icon 
    name="favorites" 
    :size="32" 
    :color="isFavorite ? '#ff6b6b' : '#cccccc'"
    :clickable="true"
    @click="toggleFavorite"
  ></flyer-icon>
  
  <!-- 自定义字体 -->
  <flyer-icon 
    name="\uE001" 
    font-family="iconfont"
    :size="24"
    color="#007aff"
  ></flyer-icon>
  
  <!-- 自定义样式 -->
  <flyer-icon 
    name="search"
    :size="20"
    color="#666666"
    :custom-style="{ marginRight: '8px' }"
  ></flyer-icon>
</template>

<script lang="uts">
export default {
  data() {
    return {
      isFavorite: false
    }
  },
  methods: {
    toggleFavorite() {
      this.isFavorite = !this.isFavorite
    }
  }
}
</script>
```

## 开发规范

### ✅ 正确用法

1. **字体图标必须在text组件中使用**
```vue
<text style="font-family: uni-icon;">\uE605</text>
```

2. **字体文件路径规范**
```css
/* 正确：使用绝对路径 */
src: url('/static/iconfont/iconfont.ttf') format('truetype');

/* 错误：使用相对路径 */
src: url('./iconfont.ttf') format('truetype');
```

3. **字体族命名规范**
```css
/* 建议：使用标准命名 */
font-family: 'iconfont';
font-family: 'uni-icon';

/* 避免：使用特殊字符 */
font-family: 'my@icon';
```

### ❌ 错误用法

1. **不能在view组件中使用字体图标**
```vue
<!-- 错误 -->
<view style="font-family: uni-icon;">\uE605</view>

<!-- 正确 -->
<text style="font-family: uni-icon;">\uE605</text>
```

2. **不要使用网络字体**
```css
/* 错误：网络路径不稳定 */
src: url('https://example.com/font.ttf');

/* 正确：本地路径 */
src: url('/static/iconfont/iconfont.ttf');
```

## 性能优化建议

1. **字体文件优化**
   - 使用.woff2格式（更小的文件大小）
   - 移除不必要的字符以减小字体文件大小
   - 设置合适的font-display值

2. **使用方式优化**
   - 优先使用内置uni-icon，避免额外字体加载
   - 合理设置图标大小，避免过大影响性能
   - 适当缓存常用图标组件

3. **加载策略**
   - 字体文件放在本地static目录
   - 设置font-display: swap提升首屏渲染速度

## 常见问题

### Q: 字体图标不显示怎么办？
A: 检查以下几点：
1. 字体文件路径是否正确
2. 是否在text组件中使用
3. Unicode字符是否正确
4. font-family设置是否正确

### Q: 如何获取iconfont的Unicode值？
A: 从iconfont网站下载字体包时会包含demo.html文件，里面有所有图标的Unicode对照表

### Q: 可以同时使用多个自定义字体吗？
A: 可以，为不同字体设置不同的font-family名称即可

### Q: 字体图标在APP上显示异常？
A: 确保字体文件格式正确，推荐使用.ttf格式，路径必须是/static开头的绝对路径
