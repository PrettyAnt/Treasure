# vue

- nodejs 安装
    - [https://nodejs.org/en/download/::](https://nodejs.org/en/download/::) 
    - npm -v
    - node -v
- 配置node.js(也可以不配置)
    - npm config set perfix "C:Program Filesnodejsnode*global"*
    - npm config set cache "C:Program Filesnodejsnode*cache"*
    - 检查配置
        - npm config get perfix 
        - npm config get cache
- 安装cnpm(淘宝镜像)
    - 管理员身份运行，cd到nodejs安装目录下
    - npm install -g cnpm --registry=[https://registry.npm.taobao.org::](https://registry.npm.taobao.org::)
    - cnpm -v 检查版本
    - npm config get registry 检查镜像是否可用
    - 重新安装cnpm
        - ①.卸载原有旧的版本：npm uninstall -g cnpm --registry=[https://registry.npm.taobao.org::](https://registry.npm.taobao.org::)
        - ②.注册模块镜像：npm set registry [https://registry.npm.taobao.org::](https://registry.npm.taobao.org::)
        - ③.node-gyp 编译依赖的 node 源码镜像npm set disturl [https://npm.taobao.org/dist::](https://npm.taobao.org/dist::)
        - ④.清空缓存npm cache clean --force
        - ⑤.重新安装cnpm npm install -g cnpm --registry=[https://registry.npm.taobao.org::](https://registry.npm.taobao.org::)
- 安装vue和vue-cli 脚手架
    - cnpm install vue -g
    - cnpm install vue-cli -g
    - vue --version 查看vue版本
    - cnpm uninstall vue-cli -g
- VSCode安装
    - 下载链接 ： [https://code.visualstudio.com/Download](https://code.visualstudio.com/Download)
- 创建VUE项目
    - 在目标目录下执行
    - vue init webpack \<项目名称 如 mywebdemo\> 注意小写
    - 依次输入 project name ： mywebdemo  Project description ： test  之后一直Enter就可以了
    - 创建完项目，cd到项目目录下
        - 执行 npm install 安装项目的依赖
    - 运行vue项目,执行 npm run dev
- 引入 element-ui
    - 到web项目下，用npm安装Element-ui npm i element-ui -S
    - 在src下的main.js中引入Element-ui

    import Vue from 'vue'
    import ElementUI from 'element-ui';             //全局引入element
    import 'element-ui/lib/theme-chalk/index.css';    //全局引入element的样式
    import App from './App.vue'
    
    Vue.config.productionTip = false
    
    Vue.use(ElementUI);     //全局注入element
    new Vue({
      render: h => h(App),
    }).$mount('#app')

- 在main.js中加入如下注释，否则会进行字符校验，非标准写法下，会编译报错
    - /* eslint-disable */
- 在Helloword.vue中写入你想要的组件

    <template>
      <div class="hello">
          <el-button type="success">成功按钮</el-button>
          <el-radio v-model="radio" label="1">备选项</el-radio>
      </div>
    </template>

- **打开vue项目**安装axios
    - npm i axios --save
- **打开vue项目**安装vue-router
    - npm install vue-router --save  用来处理页面跳转
- **打开vue项目**以上命令简写
    - npm i element-plus -S
    - npm i vue-router -S
    - npm i axios -S
- vue项目文件无法删除:[https://blog.csdn.net/weixin\_45890764/article/details/119205168](https://blog.csdn.net/weixin_45890764/article/details/119205168)
- vue3版本快速集成
    - sudo npm uninstall -g vue-cli
    - sudo  npm install -g @vue/cli
    - sudo vue creat 项目名
    - cd 项目
    - sudo npm run serve
    - sudo npm i element-plus -S
    - sudo npm i axios -S
    - sudo npm i vue-router -S

