## el-row & el-col
el-row和el-col是用于左右布局划分宽度的
el-row标签内会分为24份竖着的区域,使用el-clo的span来指定这一块内容占用几份宽度,使用offset属性可以为当前内容添加多少份偏移量空格
## el-form & el-form-item
el-form是整个表单,el-form-item是表单内的每一行
```vue
        <el-form ref="form" size="large" autocomplete="off" v-if="isRegister">
          <el-form-item>
            <h1>注册</h1>
          </el-form-item>
          <el-form-item>
            <el-input :prefix-icon="User" placeholder="请输入用户名"></el-input>
          </el-form-item>
          <el-form-item>
            <el-input :prefix-icon="Lock" type="password" placeholder="请输入密码"></el-input>
          </el-form-item>
          <el-form-item>
            <el-input :prefix-icon="Lock" type="password" placeholder="请输入再次密码"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button class="button" type="primary" auto-insert-space> 注册 </el-button>
          </el-form-item>
          <el-form-item class="flex">
            <el-link type="info" :underline="false" @click="isRegister = false"> ← 返回 </el-link>
          </el-form-item>
        </el-form>
```
绑定校验规则时,rules绑定在el-form的:rules上,再在el-form-item上绑定prop='<form里的属性名(只写属性名,不写form.<...>)>',并且这个input要和数据双向绑定好
### model
el-form有 :model属性可以绑定一个==对象==来接收所有的表单数据
```js
const formModel = ref({
  phone_number: '',
  verify_code: '',
  password: '',
  re_password: '',
})
```
### validate()
```js
const register =async () =>{
  await form.value.validate()
}
```
这个方法会对表单当前的内容再过一遍所有的校验规则,返回值是一个promise,如果校验未通过则会在页面上触发对应的校验条件的msg,通过则会继续执行
### rule
>trigger默认为change

rule可以绑定一个对象用来做数据校验,需要与el-form-input中使用v-model来绑定上对应的model中的数据,在el-form-item中的prop绑定对应的规则
```js
const rules = {
  phone_number: [
    { required: true, message: '请输入手机号', trigger: 'change' },
    {
      pattern: /^(?:(?:\+|00)86)?1[3-9]\d{9}$/,
      message: '请输入正确的手机号',
      trigger: 'blur',
    },
  ],
  verify_code: [{ required: true, message: '请输入验证码', trigger: 'change' }],
  password: [
    { required: true, message: '请输入密码', trigger: 'change' },
    {
      pattern:
        /^(?![a-zA-Z]+$)(?![A-Z0-9]+$)(?![A-Z\W_!@#$%^&*`~()-+=]+$)(?![a-z0-9]+$)(?![a-z\W_!@#$%^&*`~()-+=]+$)(?![0-9\W_!@#$%^&*`~()-+=]+$)[a-zA-Z0-9\W_!@#$%^&*`~()-+=]/,
      message: '密码应包含大写字母，小写字母，数字，特殊符号中任意3项',
      trigger: 'blur',
    },
  ],
  re_password: [
    { required: true, message: '请输入密码', trigger: 'change' },
    {
      pattern:
        /^(?![a-zA-Z]+$)(?![A-Z0-9]+$)(?![A-Z\W_!@#$%^&*`~()-+=]+$)(?![a-z0-9]+$)(?![a-z\W_!@#$%^&*`~()-+=]+$)(?![0-9\W_!@#$%^&*`~()-+=]+$)[a-zA-Z0-9\W_!@#$%^&*`~()-+=]/,
      message: '密码应包含大写字母，小写字母，数字，特殊符号中任意3项',
      trigger: 'blur',
    },
  ],
}
```
#### 自定义校验规则
```js
    {
      validator: (rule, value, callback) => {
        if (value !== formModel.value.password) {
          callback(new Error('请检查两次输入密码是否一致'))
        } else {
          callback()
        }
      },
    }
```
validator属性可以传入一个函数来进行自定义校验,函数有三个参数,rule是当前规则(一般没用),value为要校验的值,callback为回调函数,校验成功直接调用callback()即可,失败则调用callback(new Error(msg))即可,callback()是一定要调一次的,否则会导致异常
## ElMessage()
这个方法是弹出一个消息提示,里边可以直接传入一个字符串,也可以传入一个结构体,
```js
  ElMessage({
    message, //提示内容
    type: 'success', //提示类型(决定颜色)
    showClose:true, //是否显示关闭按钮
    duration:0 //持续时间,0为不自动消失
  })
```
>注意:如果使用了自动导入组件,这个不要自己导入,会丢样式,报错未定义的话去eslint里声明一下
```js
import { defineConfig, globalIgnores } from 'eslint/config'
import globals from 'globals'
import js from '@eslint/js'
import pluginVue from 'eslint-plugin-vue'
import skipFormatting from '@vue/eslint-config-prettier/skip-formatting'

export default defineConfig([
  {
    name: 'app/files-to-lint',
    files: ['**/*.{js,mjs,jsx,vue}'],
  },

  globalIgnores(['**/dist/**', '**/dist-ssr/**', '**/coverage/**']),

  {
    languageOptions: {
      globals: {
        ...globals.browser,
      },
    },
  },

  js.configs.recommended,
  ...pluginVue.configs['flat/essential'],
  skipFormatting,
  // 添加以下配置块来覆盖规则
  {
    name: 'app/vue-overrides',
    rules: {
      'vue/multi-word-component-names': 'off',
    },
    //这里的内容是用来排除自动导入的报错的
    globals: {
      ElMessage: 'readonly',
      ElMessageBox: 'readonly',
      ElLoading: 'readonly',
    },
  },
])
```
## el-menu & el-menu-item
菜单中写上router属性即可点击选项跳转路由,default-active要写item的index,也可以写$router.path来让他自己匹配