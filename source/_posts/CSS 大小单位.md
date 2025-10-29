---
title: CSS 大小单位
date: 2025-10-29 18:00:30
tags:
  - CSS
---

## 一、自动适应屏幕的单位

要实现字体大小自动适应屏幕，推荐使用以下单位：

### 1. **rem（推荐）**

- 相对于根元素（html）的字体大小
- 最常用的响应式字体单位
- 便于全局控制和维护

```css
html {
  font-size: 16px; /* 基准大小 */
}

h1 {
  font-size: 2rem; /* 32px */
}

p {
  font-size: 1rem; /* 16px */
}

/* 响应式调整 */
@media (max-width: 768px) {
  html {
    font-size: 14px; /* 所有 rem 单位自动缩放 */
  }
}
```

### 2. **vw/vh（视口单位）**

- vw：视口宽度的 1%
- vh：视口高度的 1%
- 直接跟随屏幕大小变化

```css
h1 {
  font-size: 5vw; /* 屏幕宽度的 5% */
}

/* 通常需要设置最大最小值 */
h1 {
  font-size: clamp(1.5rem, 5vw, 3rem);
}
```

### 3. **clamp()（现代最佳实践）**

- 设置最小值、首选值、最大值
- 自动在范围内响应

```css
h1 {
  font-size: clamp(1.5rem, 2vw + 1rem, 3rem);
  /* 最小 1.5rem，最大 3rem，中间根据视口调整 */
}
```

## 二、CSS 所有大小单位分类

### **绝对单位**

| 单位  | 说明                  | 使用场景                         |
| ----- | --------------------- | -------------------------------- |
| px    | 像素                  | 需要精确控制的地方（边框、阴影） |
| pt    | 点（1pt = 1/72 英寸） | 打印样式表                       |
| pc    | 派卡（1pc = 12pt）    | 打印样式表                       |
| in    | 英寸                  | 打印样式表                       |
| cm/mm | 厘米/毫米             | 打印样式表                       |

### **相对单位**

| 单位 | 相对于            | 使用场景                    |
| ---- | ----------------- | --------------------------- |
| em   | 父元素字体大小    | 局部缩放（padding、margin） |
| rem  | 根元素字体大小    | **字体大小、布局间距**      |
| %    | 父元素对应属性    | 宽度、高度                  |
| vw   | 视口宽度的 1%     | 响应式字体、全屏元素        |
| vh   | 视口高度的 1%     | 全屏高度、Hero 区域         |
| vmin | vw 和 vh 中较小的 | 正方形元素                  |
| vmax | vw 和 vh 中较大的 | 特殊响应式需求              |
| ch   | 字符 "0" 的宽度   | 限制文本行宽                |
| ex   | 字母 "x" 的高度   | 排版微调                    |

## 三、实际使用场景建议

### **字体大小**

```css
/* ✅ 推荐：rem + 媒体查询 */
html {
  font-size: 16px;
}
body {
  font-size: 1rem;
}
h1 {
  font-size: 2.5rem;
}

/* ✅ 推荐：clamp 响应式 */
h1 {
  font-size: clamp(1.5rem, 4vw, 3rem);
}

/* ⚠️ 不推荐：纯 px（不响应） */
h1 {
  font-size: 32px;
}
```

### **间距（padding/margin）**

```css
/* ✅ 推荐：rem（统一缩放） */
.container {
  padding: 2rem;
}

/* ✅ 可选：em（相对于当前字体） */
button {
  padding: 0.5em 1em;
}
```

### **宽度/高度**

```css
/* ✅ 推荐：百分比 */
.container {
  width: 80%;
}

/* ✅ 推荐：vw（全宽） */
.hero {
  width: 100vw;
}

/* ✅ 推荐：max-width 限制 */
.content {
  width: 90%;
  max-width: 1200px;
}
```

### **行宽控制**

```css
/* ✅ 推荐：ch（字符宽度） */
p {
  max-width: 65ch; /* 约 65 个字符宽度，最佳阅读体验 */
}
```

## 四、最佳实践方案

```css
/* 基础设置 */
html {
  font-size: 16px; /* 桌面基准 */
}

/* 响应式调整基准 */
@media (max-width: 1024px) {
  html {
    font-size: 15px;
  }
}

@media (max-width: 768px) {
  html {
    font-size: 14px;
  }
}

/* 使用 rem 定义字体 */
body {
  font-size: 1rem;
}
h1 {
  font-size: 2.5rem;
}
h2 {
  font-size: 2rem;
}
h3 {
  font-size: 1.5rem;
}

/* 或使用 clamp（更现代） */
h1 {
  font-size: clamp(1.75rem, 5vw, 3rem);
}
h2 {
  font-size: clamp(1.5rem, 4vw, 2.5rem);
}
```

**总结**：

- **字体大小**：优先使用 `rem` 或 `clamp()`
- **间距**：使用 `rem` 或 `em`
- **宽度**：使用 `%` 或 `vw`
- **避免使用**：纯 `px`（除非需要精确像素控制）
