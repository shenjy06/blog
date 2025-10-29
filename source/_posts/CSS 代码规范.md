---
title: CSS 代码规范
date: 2025-10-28 18:00:30
tags:
  - CSS
---

# CSS 属性书写顺序最佳实践

## 一、推荐的属性书写顺序

大厂（Google、Airbnb、Bootstrap 等）普遍遵循的顺序：

### **1. 定位属性（Positioning）**

影响元素在文档流中的位置

```css
.element {
  /* 定位 */
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  z-index: 100;

  /* 浮动 */
  float: left;
  clear: both;
}
```

### **2. 盒模型属性（Box Model）**

元素自身的尺寸和空间

```css
.element {
  /* 显示类型 */
  display: flex;

  /* 盒模型 */
  box-sizing: border-box;
  width: 100px;
  height: 100px;
  min-width: 50px;
  max-width: 200px;

  /* 内外边距 */
  padding: 10px;
  margin: 20px;

  /* 边框 */
  border: 1px solid #000;
  border-radius: 4px;

  /* 溢出 */
  overflow: hidden;
}
```

### **3. 排版属性（Typography）**

文本相关样式

```css
.element {
  /* 字体 */
  font-family: Arial, sans-serif;
  font-size: 16px;
  font-weight: 700;
  font-style: italic;
  line-height: 1.5;

  /* 文本 */
  color: #333;
  text-align: center;
  text-decoration: none;
  text-transform: uppercase;
  letter-spacing: 0.1em;
  word-spacing: 0.2em;
  white-space: nowrap;
}
```

### **4. 视觉属性（Visual）**

背景、颜色等视觉效果

```css
.element {
  /* 背景 */
  background-color: #fff;
  background-image: url('image.jpg');
  background-position: center;
  background-size: cover;
  background-repeat: no-repeat;

  /* 阴影和效果 */
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  opacity: 0.9;
  filter: blur(5px);
}
```

### **5. 其他属性（Misc）**

动画、过渡等

```css
.element {
  /* 变换 */
  transform: scale(1.1);

  /* 过渡动画 */
  transition: all 0.3s ease;
  animation: fadeIn 1s;

  /* 交互 */
  cursor: pointer;
  user-select: none;
  pointer-events: none;
}
```

## 二、完整示例

```css
.card {
  /* 1. 定位 */
  position: relative;
  z-index: 1;

  /* 2. 盒模型 */
  display: flex;
  flex-direction: column;
  width: 300px;
  height: 400px;
  padding: 20px;
  margin: 0 auto;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  overflow: hidden;

  /* 3. 排版 */
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  font-size: 16px;
  line-height: 1.6;
  color: #333;
  text-align: left;

  /* 4. 视觉 */
  background-color: #ffffff;
  background-image: linear-gradient(to bottom, #fff, #f5f5f5);
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  opacity: 1;

  /* 5. 其他 */
  transform: translateY(0);
  transition: all 0.3s ease-in-out;
  cursor: pointer;
}

.card:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 12px rgba(0, 0, 0, 0.15);
}
```

## 三、大厂规范对比

### **Google (gts)**

```css
.selector {
  /* 1. Positioning */
  position: absolute;
  top: 0;

  /* 2. Display & Box Model */
  display: block;
  width: 100px;
  padding: 10px;

  /* 3. Other */
  background: #fff;
  color: #000;
}
```

### **Airbnb CSS / Sass Style Guide**

```css
.selector {
  /* Positioning */
  position: absolute;
  z-index: 10;
  top: 0;
  right: 0;

  /* Display & Box Model */
  display: inline-block;
  overflow: hidden;
  box-sizing: border-box;
  width: 100px;
  height: 100px;
  padding: 10px;
  border: 10px solid #333;
  margin: 10px;

  /* Typography */
  font-family: sans-serif;
  font-size: 16px;
  line-height: 1.4;
  text-align: right;

  /* Visual */
  background-color: #f5f5f5;
  color: #fff;

  /* Animation */
  transition: color 1s;

  /* Misc */
  cursor: pointer;
}
```

### **Bootstrap**

```css
.btn {
  /* Display & Position */
  display: inline-block;
  position: relative;

  /* Box Model */
  padding: 0.375rem 0.75rem;
  margin-bottom: 0;

  /* Typography */
  font-weight: 400;
  font-size: 1rem;
  line-height: 1.5;
  text-align: center;
  white-space: nowrap;

  /* Visual */
  color: #212529;
  background-color: transparent;
  border: 1px solid transparent;
  border-radius: 0.25rem;

  /* Interaction */
  cursor: pointer;
  user-select: none;
  transition: all 0.15s ease-in-out;
}
```

## 四、自动化工具

### **1. Stylelint 配置**

```json
{
  "plugins": ["stylelint-order"],
  "rules": {
    "order/properties-order": [
      "position",
      "top",
      "right",
      "bottom",
      "left",
      "z-index",
      "display",
      "flex-direction",
      "justify-content",
      "align-items",
      "width",
      "height",
      "padding",
      "margin",
      "border",
      "border-radius",
      "overflow",
      "font-family",
      "font-size",
      "font-weight",
      "line-height",
      "color",
      "text-align",
      "background",
      "background-color",
      "box-shadow",
      "opacity",
      "transform",
      "transition",
      "animation"
    ]
  }
}
```

### **2. Prettier 配置**

```json
{
  "plugins": ["prettier-plugin-css-order"]
}
```

### **3. VS Code 插件**

- **CSS Peek** - 快速查看样式
- **Stylelint** - 自动检查和排序
- **CSS Navigation** - 样式导航

## 五、记忆口诀

**"定盒排视其"**

1. **定**位（Positioning）- position, top, z-index
2. **盒**模型（Box Model）- display, width, padding, margin, border
3. **排**版（Typography）- font, color, text-align
4. **视**觉（Visual）- background, shadow, opacity
5. **其**他（Others）- transform, transition, animation

## 六、实际工作建议

### ✅ **推荐做法**

- 使用 Stylelint + 自动格式化工具
- 团队统一配置文件
- 代码审查时检查顺序
- 使用 CSS-in-JS 时也遵循顺序

### ⚠️ **注意事项**

- 不必过度纠结，保持一致性最重要
- 简写属性（如 `background`）放在对应分类中
- 浏览器前缀属性紧跟标准属性后面
- 同类属性按字母排序（可选）

### 📋 **团队协作**

```javascript
// .stylelintrc.js
module.exports = {
  extends: ['stylelint-config-standard'],
  plugins: ['stylelint-order'],
  rules: {
    'order/properties-alphabetical-order': null,
    'order/properties-order': [
      // 你的团队约定顺序
    ],
  },
}
```

**总结**：大厂通用顺序是 **定位 → 盒模型 → 排版 → 视觉 → 其他**，最重要的是团队统一并使用自动化工具强制执行。

## CSS 代码规范

- https://google.github.io/styleguide/htmlcssguide.html
- https://github.com/airbnb/css
- https://airbnb.io/
