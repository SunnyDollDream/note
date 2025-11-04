>Tailwind CSS 是一个工具优先的框架，意味着它提供了大量的预定义类，而不是预设的组件,这使得开发者可以构建几乎任何设计，而不需要编写 CSS。
## 安装
### CDN
- 使用官网提供的 CDN 库，地址如下：
```html
<script src="https://cdn.tailwindcss.com"></script>  
```
- 使用 jsDelivr 的 CDN 库，地址如下：
```html
<link href="https://cdn.jsdelivr.net/npm/tailwindcss@3.3.0/dist/tailwind.min.css" rel="stylesheet">
```
### NPM
通过 npm 安装 在本地项目中安装并配置 Tailwind CSS：
```shell
npm install tailwindcss @tailwindcss/cli
```
**1、在 CSS 中导入 Tailwind**: 在 CSS 文件中添加 @import "tailwindcss";。
比如在 src/index.css 文件开头导入：
```css
@import "tailwindcss";  
```
**2、启动 Tailwind CLI 构建流程**：运行 CLI 工具以扫描源文件中的类并构建 CSS。
```shell
npx @tailwindcss/cli -i ./src/input.css -o ./src/output.css --watch
```
**3、在 HTML 中开始使用 Tailwind**：将编译后的 CSS 文件添加到 `<head>` 中，并开始使用 Tailwind 的实用类来设置内容的样式。
```html
<!doctype html>  
<html>  
<head>  
  <meta charset="UTF-8">  
  <meta name="viewport" content="width=device-width, initial-scale=1.0">  
  <link href="./output.css" rel="stylesheet">  
</head>  
<body>  
  <h1 class="text-3xl font-bold underline">  
    Hello world!  
  </h1>  
</body>  
</html>
```
### Vue3集成
#### v3
>[Vue 3 项目中引入 Tailwind CSSVue 3 项目中引入 Tailwind CSS 1. 安装依赖 在项目 - 掘金](https://juejin.cn/post/7481187555867115520)
1. **安装依赖**
在项目根目录下运行以下命令，安装 Tailwind CSS 及其依赖项：
```shell
npm install -D tailwindcss@3 postcss autoprefixer
```
2. **生成配置文件**
运行以下命令生成 `tailwind.config.js` 和 `postcss.config.js` 文件：
```shell
npx tailwindcss init -p
```
3. **配置 Tailwind**
编辑生成的 `tailwind.config.js` 文件，配置 `content` 属性，指定 Tailwind 应扫描的文件路径：
- tailwind.config.js
	这是一个 Tailwind CSS 的配置文件，你可以在这里自定义 Tailwind 的行为，例如配置主题、扩展样式、添加插件等。默认生成的内容如下
```css
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{vue,js,ts,jsx,tsx}"
  ],
  theme: {
    extend: {}
  },
  plugins: []
};

```
- postcss.config.js
	这是一个 PostCSS 的配置文件，用于指定 PostCSS 的插件和配置。默认生成的内容如下：
```css
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
};
```
4. **创建 Tailwind 样式文件**
在 `src` 目录下创建一个 `index.css` 文件，并添加以下内容
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```
5. **引入 Tailwind 样式**
在 `src/main.js` 或 `src/main.ts` 文件中引入 `index.css`
```ts
import './index.css';
```
6. **使用 Tailwind**
现在可以在 Vue 组件中直接使用 Tailwind 的类名。例如
```vue
<template>
  <div class="text-3xl font-bold underline">Hello Tailwind!</div>
</template>
```
#### v4
1. **安装依赖**
	Tailwind CSS v4 不再需要单独配置 PostCSS。如果你使用的是 Vite，可以直接安装 `@tailwindcss/vite` 插件。运行以下命令：
```shell
npm install @tailwindcss/vite
```
2. **更新 Vite 配置**
	在 `vite.config.js` 中，确保你引入了 `@tailwindcss/vite` 插件
```js
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  plugins: [
    vue(),
    tailwindcss()
  ]
});
```
3. **创建 Tailwind 样式文件**
>从 Tailwind CSS v4 开始，==`npx tailwindcss init` 命令已被废弃==。如果你安装了 Tailwind CSS v4 或更高版本，建议直接在 CSS 文件中配置 Tailwind，而不是通过 `tailwind.config.js` 文件。

在 `src` 目录下创建一个 `index.css` 文件，并添加以下内容
```css
@import "tailwindcss";
```
4. **引入 Tailwind 样式**
在 `src/main.js` 或 `src/main.ts` 文件中引入 `index.css`
```ts
import './index.css';
```
5. **使用 Tailwind**
现在可以在 Vue 组件中直接使用 Tailwind 的类名。
```vue
<template>
  <div class="text-3xl font-bold underline">Hello Tailwind!</div>
</template>
```
## 基本概念
>[Tailwind CSS 基础概念 | 菜鸟教程](https://www.runoob.com/tailwindcss/tailwindcss-basic.html)
## JIT
TailwindCSS有一个JIT(即时编译)机制,它只会生成你在项目中所用到的工具类,而不是把所有的工具类一次性导入
### 优点
1. 优化性能,只生成需要的内容
2. 缩短构建时间,因为它不需要预编译成千上万的类
3. 它使工具类可以支持任意值
4. 开发和生产环境无缝运行
5. TailwindCSS内置,无需额外配置
 