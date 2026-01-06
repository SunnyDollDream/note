# uni-app 基础

## 创建 uni-app 项目方式

**uni-app 支持两种方式创建项目：**

1. 通过 HBuilderX 创建（需安装 HBuilderX 编辑器）

2. 通过命令行创建（需安装 NodeJS 环境）

## HBuilderX 创建 uni-app 项目

**1.下载安装 HbuilderX 编辑器**

![下载安装 HbuilderX 编辑器](./assets/uniapp_picture_1.png)

**2.通过 HbuilderX 创建 uni-app vue3 项目**

![通过 HbuilderX 创建 uni-app vue3 项目](./assets/uniapp_picture_2.png)

**3.安装 uni-app vue3 编译器插件**

![安装 uni-app vue3 编译器插件](./assets/uniapp_picture_3.png)

**4.编译成微信小程序端代码**

![编译成微信小程序端代码](./assets/uniapp_picture_4.png)
>需要在uniapp项目的manifest.json中配置微信小程序的appid,否则会导致更新无法推送到开发者工具

**5.开启服务端口**

![开启服务端口](./assets/uniapp_picture_5.png)

**小技巧分享：模拟器窗口分离和置顶**

![模拟器窗口分离和置顶](./assets/uniapp_picture_6.png)

**HBuildeX 和 微信开发者工具 关系**

![HBuildeX 和 微信开发者工具 关系](./assets/uniapp_picture_7.png)

[HBuildeX](https://www.dcloud.io/hbuilderx.html) 和 [uni-app](https://uniapp.dcloud.net.cn/) 都属于 [DCloud](https://dcloud.io) 公司的产品。
## pages.json 和 tabBar 案例

### 目录结构

我们先来认识 uni-app 项目的目录结构。

```text
├─pages            业务页面文件存放的目录
│  └─index
│     └─index.vue  index页面
├─static           存放应用引用的本地静态资源的目录(注意：静态资源只能存放于此)
├─unpackage        非工程代码，一般存放运行或发行的编译结果
├─index.html       H5端页面
├─main.js          Vue初始化入口文件
├─App.vue          配置App全局样式、监听应用生命周期
├─pages.json       配置页面路由、导航栏、tabBar等页面类信息
├─manifest.json    配置appid、应用名称、logo、版本等打包信息
└─uni.scss         uni-app内置的常用样式变量
```
>静态资源必须放在static里,否则无法打包
### 解读 pages.json

用于配置页面路由、导航栏、tabBar 等页面类信息

### 案例练习

**效果预览**
![案例练习](./assets/uniapp_case_1.png)

**参考代码**

```json
{
  // 页面路由
  "pages": [
    {
      "path": "pages/index/index",
      // 页面样式配置
      "style": {
        "navigationBarTitleText": "首页" //小程序上方导航栏的标题
      }
    },
    {
      "path": "pages/my/my",
      "style": {
        "navigationBarTitleText": "我的"
      }
    }
  ],
  // 全局样式配置
  "globalStyle": {
    "navigationBarTextStyle": "white", // 导航栏文字颜色(只支持黑/白)
    "navigationBarTitleText": "uni-app",
    "navigationBarBackgroundColor": "#27BA9B", //导航栏的背景颜色
    "backgroundColor": "#F8F8F8"
  },
  // tabBar 配置
  "tabBar": {
    "selectedColor": "#27BA9B", // 选中时的文字颜色
    "list": [
      {
        "pagePath": "pages/index/index",
        "text": "首页", // tabBar的文本
        "iconPath": "static/tabs/home_default.png", // tabBar的图标
        "selectedIconPath": "static/tabs/home_selected.png" // 选中后的图标
      },
      {
        "pagePath": "pages/my/my",
        "text": "我的",
        "iconPath": "static/tabs/user_default.png",
        "selectedIconPath": "static/tabs/user_selected.png"
      }
    ]
  }
}
```

## uni-app 和原生小程序开发区别

### 开发区别

uni-app 项目每个页面是一个 `.vue` 文件，数据绑定及事件处理同 `Vue.js` 规范：

1. 属性绑定 `src="{ { url }}"` (微信原生)升级成 `:src="url"`(Vue)

2. 事件绑定 `bindtap="eventName"` 升级成 `@tap="eventName"`，**支持（）传参**

3. 支持 Vue 常用**指令** `v-for`、`v-if`、`v-show`、`v-model` 等

### 其他区别补充

1. 调用接口能力，**建议前缀** `wx` 替换为 `uni` ，养成好习惯，**支持多端开发**。
2. `<style>` 页面样式不需要写 `scoped`，小程序是多页面应用，**页面样式自动隔离**。
3. **生命周期分三部分**：应用生命周期(小程序)，页面生命周期(小程序)，组件生命周期(Vue)

### 案例练习

**主要功能**

1.  滑动轮播图
2.  点击大图预览

**效果预览**
![案例练习](./assets/uniapp_case_2.png)

**参考代码**

```vue
<template>
  <swiper class="banner" indicator-dots circular :autoplay="false">
    <swiper-item v-for="item in pictures" :key="item.id">
      <image @tap="onPreviewImage(item.url)" :src="item.url"></image>
    </swiper-item>
  </swiper>
</template>

<script>
export default {
  data() {
    return {
      // 轮播图数据
      pictures: [
        {
          id: '1',
          url: 'https://pcapi-xiaotuxian-front-devtest.itheima.net/miniapp/uploads/goods_preview_1.jpg',
        },
        {
          id: '2',
          url: 'https://pcapi-xiaotuxian-front-devtest.itheima.net/miniapp/uploads/goods_preview_2.jpg',
        },
        {
          id: '3',
          url: 'https://pcapi-xiaotuxian-front-devtest.itheima.net/miniapp/uploads/goods_preview_3.jpg',
        },
        {
          id: '4',
          url: 'https://pcapi-xiaotuxian-front-devtest.itheima.net/miniapp/uploads/goods_preview_4.jpg',
        },
        {
          id: '5',
          url: 'https://pcapi-xiaotuxian-front-devtest.itheima.net/miniapp/uploads/goods_preview_5.jpg',
        },
      ],
    }
  },
  methods: {
    onPreviewImage(url) {
      // 大图预览
      uni.previewImage({
        urls: this.pictures.map((v) => v.url),
        current: url,
      })
    },
  },
}
</script>

<style>
.banner,
.banner image {
  width: 750rpx;
  height: 750rpx;
}
</style>
```
>这里的`<swiper>`是uniapp自己组件库里的组件,在uniapp中可以使用uniapp的api和组件,也可以直接使用微信小程序的api和组件,两者接口是一致的
## 命令行创建 uni-app 项目

**优势**

通过命令行创建 uni-app 项目，**不必依赖 HBuilderX**，TypeScript 类型支持友好。

**命令行创建** **uni-app** **项目：**
vue3 + js
```shell
npx degit dcloudio/uni-preset-vue#vite <项目名称>
```
vue3 + ts 版
```shell
# 通过 npx 从 github 下载
npx degit dcloudio/uni-preset-vue#vite-ts <项目名称>
```

```sh [👉国内 gitee]
# 通过 git 从 gitee 克隆下载 (👉备用地址)
git clone -b vite-ts https://gitee.com/dcloud/uni-preset-vue.git
```
vue2
```shell
# 需要全局安装 vue-cli
npm install -g @vue/cli

vue create -p dcloudio/uni-preset-vue <项目名称>
```
创建其他版本可查看：[uni-app 官网](https://uniapp.dcloud.net.cn/quickstart-cli.html)
### 编译和运行 uni-app 项目

1. 安装依赖 `pnpm install`
2. 编译成微信小程序 `pnpm dev:mp-weixin`
3. 导入微信开发者工具
此时还是支持hmr的

>编译成 H5 端可运行 `pnpm dev:h5` 通过浏览器预览项目。
## 用 VS Code 开发 uni-app 项目

### 为什么选择 VS Code？

- VS Code 对 **TS 类型支持友好**，前端开发者**主流的编辑器**
- HbuilderX 对 TS 类型支持暂不完善
### 用 VS Code 开发配置

- 👉 前置工作：安装 Vue3 插件，[点击查看官方文档](https://cn.vuejs.org/guide/typescript/overview.html#ide-support)
  - 安装 **Vue Language Features (Volar)** ：Vue3 语法提示插件
  - 安装 **TypeScript Vue Plugin (Volar)** ：Vue3+TS 插件
  - **工作区禁用** Vue2 的 Vetur 插件(Vue3 插件和 Vue2 冲突)
  - **工作区禁用** @builtin typescript 插件（禁用后开启 Vue3 的 TS 托管模式）
- 👉 安装 uni-app 开发插件
  - **uni-create-view** ：快速创建 uni-app 页面(不好用)
  - **uni-helper uni-app** ：代码提示
  - **uniapp 小程序扩展** ：鼠标悬停查文档
---
**不要配TS和JSON了,没什么用而且很麻烦,JSON可以直接把注释删掉,反正也没用**
- ~~👉 TS 类型校验~~
  - ~~安装 **类型声明文件** `pnpm i -D miniprogram-api-typings @uni-helper/uni-app-types`~~
>注意这里有问题,这两个类型依赖需要ts5以上,但是在uniapp的模板中使用的ts4,去package.json中ts的版本改为5.0.0,然后重新npm i更新ts版本,但是此时ts5中弃用了模板中已有的一些配置项,打开tsConfig,ctrl点击最上边的extends后边的文件,去里边把`preserveValueImports`和`importsNotUsedAsValues`这两个选项注释掉,回到tsconfig中在`compilerOptions`中新增`"verbatimModuleSyntax": true`解决报错问题
>还有就是`vueCompilerOptions`中的`experimentalRuntimeMode`和`nativeTags`这两个属性都被删了(`vueCompilerOptions`本身里边就有很多实验性选项,更新很频繁),需要按下边的tsconfig.json例子配置plugin选项

  - ~~配置 `tsconfig.json`~~
- ~~👉 JSON 注释问题~~
  - ~~设置文件关联，把 `manifest.json` 和 `pages.json` 设置为 `jsonc`~~

`tsconfig.json` 参考

```json
// tsconfig.json
{
  "extends": "@vue/tsconfig/tsconfig.json",
  "compilerOptions": {
    "sourceMap": true,
    "baseUrl": ".",
    "paths": {
      "@/*": [
        "./src/*"
      ]
    },
    "lib": [
      "esnext",
      "dom"
    ],
    "types": [
      "@dcloudio/types",
      "miniprogram-api-typings",
      "@uni-helper/uni-app-types"
    ],
    "verbatimModuleSyntax": true
  },
  "vueCompilerOptions": {
    "plugins": [
      "@uni-helper/uni-app-types/volar-plugin"
    ]
  },
  "include": [
    "src/**/*.ts",
    "src/**/*.d.ts",
    "src/**/*.tsx",
    "src/**/*.vue"
  ]
}
```

**工作区设置参考**

```json
// .vscode/settings.json
{
  // 在保存时格式化文件
  "editor.formatOnSave": true,
  // 文件格式化配置
  "[json]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  // 配置语言的文件关联
  "files.associations": {
    "pages.json": "jsonc", // pages.json 可以写注释
    "manifest.json": "jsonc" // manifest.json 可以写注释
  }
}
```
## 开发工具回顾

选择自己习惯的编辑器开发 uni-app 项目即可。

**HbuilderX 和 微信开发者工具 关系**
![HbuilderX 和 微信开发者工具 关系](./assets/uniapp_picture_7.png)

**VS Code 和 微信开发者工具 关系**
![VS Code 和 微信开发者工具 关系](./assets/uniapp_picture_8.png)
# uni-app API
## uni-ui 组件库
uniapp本身就自带一些组件,这个组件库是官方的拓展组件库
**安装**
```shell
pnpm i @dcloudio/uni-ui
```
**配置自动导入组件**
使用 `npm` 安装好 `uni-ui` 之后，需要配置 `easycom` 规则，让 `npm` 安装的组件支持 `easycom`(自动导入)
打开项目根目录下的 `pages.json` 并添加 `easycom` 节点：
```json
// pages.json
{
	"easycom": {
		"autoscan": true, //是否开启自动扫描
		"custom": {
			// uni-ui 规则如下配置
			"^uni-(.*)": "@dcloudio/uni-ui/lib/uni-$1/uni-$1.vue"
		}
	},
	
	// 其他内容
	pages:[
		// ...
	]
}
```
**安装类型声明文件**
```bash
pnpm i -D @uni-helper/uni-ui-types
```
**配置类型声明文件**
```json
{
  "extends": "@vue/tsconfig/tsconfig.json",
  "compilerOptions": {
    "allowJs": true,
    "sourceMap": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    },
    "lib": ["esnext", "dom"],
    "types": [
      "@dcloudio/types",
      "miniprogram-api-typings",
      "@uni-helper/uni-app-types",
      "@uni-helper/uni-ui-types"
    ]
  },
  "vueCompilerOptions": {
    // experimentalRuntimeMode 已废弃，请升级 Vue - Official 插件至最新版本
    "plugins": ["@uni-helper/uni-app-types/volar-plugin"]
  },
  "include": ["src/**/*.ts", "src/**/*.d.ts", "src/**/*.tsx", "src/**/*.vue"]
}
```
## 小程序端 Pinia 持久化
在uniapp中存储使用的为uni.setStorageSync()和.get~(),需要更换`pinia-plugin-persistedstate`中的存储介质
```ts
import { defineStore } from 'pinia'
import { ref } from 'vue'

// 定义 Store
export const useMemberStore = defineStore(
  'member',
  () => {
    // 会员信息
    const profile = ref<any>()

    // 保存会员信息，登录时使用
    const setProfile = (val : any) => {
      profile.value = val
    }

    // 清理会员信息，退出时使用
    const clearProfile = () => {
      profile.value = undefined
    }

    // 记得 return
    return {
      profile,
      setProfile,
      clearProfile,
    }
  },
  // TODO: 持久化
  {
    persist: {
      storage: {
        getItem(key) {
          return uni.getStorageSync(key)
        },
        setItem(key, value) {
          uni.setStorageSync(key, value)
        }
      }
    },
  },
)
```
## uni.request 请求封装
因为跨端需要,原先使用axios负责的http请求在uniapp中需要使用uni.request,拦截器使用uni.addInterceptor
**封装请求**
1. 使用TS泛型,方便类型校验
2. uni.request(options,success(),fail()),其中option为请求时的配置,如url和请求方式,后边两个是响应成功和无响应的回调,注意是响应成功就会调用success,即404等错误相应也会调用success,所以需要在success中判断,fail只有当完全没有响应的时候才会调用
```ts
interface Data<T> {
  code: string
  msg: string
  result: T
}

export const request = <T>(options: UniApp.RequestOptions) => {
  return new Promise<Data<T>>((resolve, reject) => {
    uni.request({
      ...options,
      success(res) {
        if (res.statusCode >= 200 && res.statusCode < 300) {
          resolve(res.data as Data<T>)
        } else if (res.statusCode === 401) {
          const memberStore = useMemberStore()
          memberStore.clearProfile()
          uni.navigateTo({ url: '/pages/login/login' })
          reject(res)
        } else {
          uni.showToast({
            icon: 'none',
            title: (res.data as Data<T>).msg || '请求错误',
          })
          reject(res)
        }
      },
      fail(err) {
        uni.showToast({
          icon: 'none',
          title: '网络错误',
        })
        reject(err)
      },
    })
  })
}
```
**使用**
```ts
const testRequest = async () => {
  const res = await request<string[]>({
    method: 'GET',
    url: '/11',
  })
  console.log(res)
}
```
## uni.addInterceptor
```ts
const baseURL = 'https://pcapi-xiaotuxian-front-devtest.itheima.net'

uni.addInterceptor('request', { //这里的'request'是指拦截的是请求,其余的还有uploadFile
  invoke(options: UniApp.RequestOptions) {
    const memberStore = useMemberStore()

    options.url = baseURL + options.url
    options.timeout = 10000
    options.header = { ...options.header, 'source-client': 'miniapp' }
    options.header.Authorization = memberStore.profile?.token || ''
  },
})
```

## uni.navigateTo
编程式路由
```ts
uni.navigateTo({ url: '/pages/login/login' })
```
## uni.showToast
Toast提示
```ts
        uni.showToast({
          icon: 'none',
          title: '网络错误',
        })
```