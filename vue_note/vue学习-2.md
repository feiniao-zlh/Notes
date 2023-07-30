### Axios网络请求

[Axios]: https://www.axios-http.cn/docs/intro

```
cnpm install --save axios
```

局部引用，即该组件使用

```vue
<script>
import axios from 'axios';
</script> 
```

全局引用，在`main.js`下

```js
import { createApp } from 'vue'
import App from './App.vue'
import './registerServiceWorker'
// 引入
import axios from 'axios';

//创建一个app对象
const app = createApp(App);
// 挂载全局
app.config.globalProperties.$axios = axios

// 组件中的调用方式
// this.$axios = axios

app.mount('#app')
```



#### get请求

```vue
<template>
  <div>
    <p>{{ chengpin.title }}</p>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'HelloWorld',
  data(){
    return{
      chengpin:{}
    }
  },
  mounted(){
    axios({
      method:"get",
      url:"http://iwenwiki.com/api/blueberrypai/getChengpinDetails.php"
    }).then(res=>{
      console.log(res.data);
      // 获取数据 注意类型
      this.chengpin = res.data.chengpinDetails[0]
    })
  }
}
</script> 

<style scoped>

</style>

```

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/get%E8%AF%B7%E6%B1%82.png)



#### post请求

```vue
<template>
  <div>
    <p>{{ chengpin.title }}</p>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'HelloWorld',
  data(){
    return{
      chengpin:{}
    }
  },
  mounted(){
    axios({
      method:"post",
      url:"http://iwenwiki.com/api/blueberrypai/login.php",
      // 参数配置
      data: {
        user_id: "iwen@qq.com",
        password: "iwen123",
        verification_code: "crfvw"
      }
    }).then(res=>{
      console.log(res.data);
    })
  }
}
</script> 

<style scoped>
</style>

```

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/post-%E5%8F%82%E6%95%B0%E7%BC%BA%E5%A4%B1.png)

错误原因：传过去的data是个对象，需要转换成字符串类型

修正方法：引入包`querystring`

```
cnpm install --save querystring
```

```vue
<script>
import querystring from 'querystring';
    
    data:querystring.stringify({
        user_id: "iwen@qq.com",
        password: "iwen123",
        verification_code: "crfvw"
      })
</script>
//运行成功 success:true……………………
```



#### 快速请求

`axios.get`

```vue
<template>
  <div>
  </div>
</template>

<script>
import axios from 'axios';
import querystring from 'querystring';

export default {
  name: 'HelloWorld',
  mounted(){    			          axios.get("http://iwenwiki.com/api/blueberrypai/getChengpinDetails.php").then(res=>{
      console.log(res.data);
    })
  }
}
</script> 

<style scoped>

</style>

```

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/%E5%BF%AB%E6%8D%B7-get.png)

`axios.post`

```vue
<template>
  <div>
  </div>
</template>

<script>
import axios from 'axios';
import querystring from 'querystring';

export default {
  name: 'HelloWorld',
  mounted(){
    axios.post("http://iwenwiki.com/api/blueberrypai/login.php",querystring.stringify({
      user_id: "iwen@qq.com",
      password: "iwen123",
      verification_code: "crfvw"}
    )).then(res=>{
      console.log(res.data);
    })
  }
}
</script> 

<style scoped>
</style>

```

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/%E5%BF%AB%E6%8D%B7-post.png)

### 网络请求封装

日常应用中，一个项目的网络请求会很多，此时会将网络请求封装

[参考文档]: https://www.kancloud.cn/yunye/axios/234845/

在`src`下创建文件夹`utils`（放置网络请求方法），在该文件夹下创建`request.js`

```js
import axios from 'axios';
import { error, log } from 'console';
import { config } from 'process';
import querystring from 'querystring';

// 错误处理函数
const errorHandle = (status,info) =>{
    switch(status){
        case 400:
            console.log("语义有误");
            break;
        case 401:
            console.log("服务器认证失败");
            break;
        case 403:
            console.log("服务器拒绝访问");
            break;
        case 404:
            console.log("地址错误");
            break;
        case 500:
            console.log("服务器遇到意外");
            break;
        case 502:
            console.log("服务器无响应");
            break;
        default:
            console.log(info);
            break;
    }
}

const instance = axios.create({
    // 网络请求的公共配置
    // 超时5s
    timeout:5000
})

// 拦截器

// 发送数据之前拦截
instance.interceptors.request.use(
    // 成功函数
    config =>{
        if(config.method =='post'){
            // 直接在这里进行post参数的格式转换,就不需要在组件里面进行了
            querystring.stringify(config.data)
        }
        // config包含着网络请求的所有信息
        return config;
    },

    // 失败函数
    error =>{
        return Promise.reject(error)
    }
)

// 获取数据之前拦截
instance.interceptors.response.use(
    // 成功
    response =>{
        // 请求是否200,网路请求是成功的
        // 信息都是response,但接收方式不一样
        return response.status == 200 ? Promise.resolve(response):Promise.reject(response)
    },
    // 网络请求失败失败
    error => {
        // response错误信息
        const {response} = error;
        errorHandle(response.status,response.info)
    }
)

// 导出
export default instance;
```



在`src`下创建文件夹`api`（放置网络请求)，该文件中创建`path.js`和`index.js`

```js
// path.js
const base = {
    // 放置所有路径
    // 公共路径
    baseUrl:"http://iwenwiki.com",

    chengpin:"/api/blueberrypai/getChengpinDetails.php"
}

export default base;
```



```js
// idnex.js 
// 放置所有网络请求方法
import axios from "../utils/request";
import path from "./path"

const api = {
    // 网络请求方案
    // 成品详情地址
    getChengpin(){
        return axios.get(path.baseUrl + path.chengpin)
    }
}

export default api
```

直接组件中通过`api`调用

```vue
<template>
  <div>
  </div>
</template>

<script>
import api from '@/api';

export default {
  name: 'HelloWorld',
  mounted(){
    api.getChengpin().then(res=>{
      ;console.log(res.data);
    })
  }
}
</script> 

<style scoped>
</style>
```



### 网络请求跨域

同源策略是浏览器的一项安全策略，浏览器只允许`js`代码请求和当前所在服务器域名,端口,协议相同的数据接口上的数据,这就是同源策略.
也就是说，当协议、域名、端口任意一个不相同时，都会产生跨域问题

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/%E8%B7%A8%E5%9F%9F%E5%87%BA%E9%94%99.png)

解决方式：

后台解决：`cors`

前台解决：`proxy`

在`config.js`文件下添加

```js
const { defineConfig } = require('@vue/cli-service')
module.exports = defineConfig({
  transpileDependencies: true,
  // 添加以下
  devServer:{
    proxy:{
      '/api':{
        // 网址地址
        // http协议 ,端口默认80,域名iwenwiki.com
        target:'http://iwenwiki.com',
        chanegeOrigin:true
      }
    }
  }
})
```

组件中调用

```vue
<template>
  <div>
    <p>{{ message }}</p>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data(){
    return{
      message:{}
    }
  },
  mounted(){ 
    // 调用
    this.$axios.get("/api/FingerUnion/list.php").then(res=>{
      console.log(res.data);
      this.message=res.data;
    })
  }
}
</script> 

<style scoped>
</style>
```



### vue引入路由配置

在创建`vue`项目的时候选择`router`即可，不需要手动设置

以下为过程

引包

```
cnpm install --save vue-router
```

配置独立文件，在`src`下新建文件夹`router`，在该文件夹下新建文件`index.js`

```js
// 路由配置文件

import {createRouter,createWebHashHistory} from 'vue-router';
import HomeView from '../views/HomeView.vue'
// 存放页面
const routes =[ 
    // 每个页面都是一个对象
    {
        // 首页通过/访问
        path:"/",
        name:'home',
        component:HomeView
    },
    {
        path:"/about",
        name:'about',
        // 异步加载方式，这样跳转的时候才引入资源，不用一开始就引入资源
        component:() => import('../views/AboutView.vue')
    }
]
// 创建路由
// 配置信息中需要页面的相关配置
const router = createRouter({
    /**
     * createWebHistory
     * home:http://localhost:8080/
     * about"http://localhost:8080/about
     * 此种方式，需要后台配合做重定向，否则出现404问题
     * 原理：H5的pushState()
     */
    /**
     * createWebHashHistory
     * home:http://localhost:8080/#/
     * about:http://localhost:8080/#/about
     * 原理：a标签的锚点链接
     */
    history:createWebHashHistory(),
    routes,
})

// 导出 
export default router;
```

在`src`下创建文件夹`views`（页面），该文件夹下创建`HomeView`和`AboutView`

```vue
<!-- HomeView -->
<template>
    <h3>首页</h3>
</template>
<!-- AboutView -->
<template>
    <h3>第二页</h3>
</template>
```

`main.js`中引用

```js
import router from './router';
app.use(router)
```

`app.vue`中使用

```vue
<template>  
  <!--使用 router-link 组件进行导航 -->
  <!--通过传递 `to` 来指定链接 -->
  <router-link to="/">首页</router-link>|
  <router-link to="/about">第二页</router-link>

  <!-- 路由匹配到的组件将渲染在这里 -->
  <router-view></router-view>

</template>

<script>
export default {
  name: 'App',
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```



### 路由传递参数

在路由配置中指定参数`key`

```\
  {
    //   /:传入参数
    path:'/news/:name',
    name:'name',
    component:() => import('../views/NewsView.vue')
  }
```

在跳转过程中携带参数

```vue
<template>
  <div>
    <ul>
      <!-- /携带参数 -->
      <li><router-link to="/news/网易">网易</router-link></li>
      <li><router-link to="/news/百度">百度</router-link></li>
      <li><router-link to="/news/小米">小米</router-link></li>
    </ul>
  </div>
</template>
```

在详情页面读取路由携带的参数

```vue
<template>
    <h3>新闻</h3>
    <!-- name为取的参数名 -->
    <p>{{ $route.params.name }}</p>
</template>
```

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/%E8%B7%AF%E7%94%B1%E8%B7%B3%E8%BD%AC.png)

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/%E8%B7%AF%E7%94%B1%E8%B7%B3%E8%BD%AC-%E6%90%BA%E5%B8%A6%E5%8F%82%E6%95%B0.png)



### 嵌套路由配置

创建子页面，`views`下创建文件夹`AboutSub`，再创建页面

`router/index.js`中父页面引入`children=[]`

```vue
  {
    path: '/about',
    name: 'about',
    // 重定向，默认进入about页面时进入info页面
    redirect:'/about/info',
    component: () => import('../views/AboutView.vue'),
    children:[
      {
        // 二级导航的路径不要加/
        path:"info",
        component:()=> import('../views/AboutSub/AboutInfo.vue')
      }
    ]
  },
```

引用

```vue
<template>
  <div>
    <!-- 此时要写全路径 -->
    <router-link to="/about/info">info</router-link>
    <router-view></router-view>
  </div>
</template>

```

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/%E8%B7%AF%E7%94%B1%E5%B5%8C%E5%A5%97.png)







### vue状态管理（vuex）

同`router`可以直接创建

以下为创建不步骤

简易理解，相当与给组件们提供一个‘数据库’，这样组件的增删并不会影响数据

安装

```
cnpm install --save vuex
```

配置

`src`下创建文件夹`store`，再创建`index.js`文件

```js
import { createStore } from "vuex";

export default createStore({
    // 所有的状态都放在这里（数据）
    state:{
        counter:0,
    }
})
```

`main,js`引入

```js
import store from './store'

const app = createApp(App)
app.use(router)
app.use(store)
```

使用

第一种读取方式

```vue
<template>
  <div>
    <ul>
      <!-- 第一种读取方式 -->
      <li>conter = {{ $store.state.counter }}</li>
    </ul>
  </div>
</template>
```

第二种读取方式

```js
<template>
  <div>
    <ul>
      <!-- 直接用counter读取 -->
      <li>conter = {{ counter }}</li>
    </ul>
  </div>
</template>

<script>
import {mapState} from 'vuex';
export default{
  // 专门读取vuex的数据
  computed:{
    ...mapState(["counter"])
  }
}
</script>
```

![](E:\learn-java\java\vue\learn\img\vuex.png)

### vuex状态管理核心

#### Getter

对``vuex`中的数据进行过滤

```js
import { createStore } from "vuex";

export default createStore({
    // 所有的状态都放在这里（数据）
    state:{
        counter:0,
    },
    getters:{
        // 自创函数
        getCounter(state){
            return state.counter > 0 ? state.counter : "counter数据异常"
        }
    }
})
```

读取

第一种方式

```vue
<template>
  <div>
    <ul>
      <!-- 直接用counter读取 -->
      <li>conter = {{ $store.getters.getCounter }}</li>
    </ul>
  </div>
</template>
```

第二种方式

```vue
<template>
  <div>
    <ul>
      <!-- 直接用counter读取 -->
      <li>conter = {{ getCounter }}</li>
    </ul>
  </div>
</template>
<script>
import {mapState} from 'vuex';
import {mapGetters} from 'vuex';

export default{
  computed:{
    ...mapGetters(["getCounter"])
  }
}
</script>
```

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/vuex-getters.png)

#### Mutation（同步）

实时更改`vuex`中的状态(数据)

```js
    mutations:{
        // 自建函数 通过事件调用 
        addCounter(state,num){
            state.counter+=num
        }
    }
})
```

引用

```vue
<template>
  <!-- 实现事件 -->
  <button @click="addCounterHandle">counter++</button>
  <p>counter = {{ getCounter }}</p>
</template>

<script>
import {mapState} from 'vuex';
import {mapGetters,mapMutations} from 'vuex';

export default{
  computed:{
    ...mapGetters(["getCounter"]),
    ...mapState(["counter"])
  },
  methods:{
    ...mapMutations(["addCounter"]),
    addCounterHandle(){
      this.addCounter(15)
    }
  }
}
</script>
```

#### Action（异步）

通常用于网络请求时

`action`类似`mutation`，区别在于

`Action`提交的是`Mutation`，而不是直接变更状态

`Action`可以包含任意异步操作

```js
import axios from "axios";

mutations:{
        // 自建函数 通过事件调用 
        addCounter(state,num){
            state.counter+=num
        }
    },
    // 为异步操作所准备的
    actions:{
        // 自建函数
        // {参数} 对象结构赋值
        asyncAddCounter({commit}){
            axios.get("http://iwenwiki.com/api/generator/list.php").then(res=>{
                // 使用方式
                commit("addCounter",res.data[0])
            })
        }
    }
```

引用

```vue
<template>
  <p>counter = {{ getCounter }}</p>
  <button @click="addCounterHandle">counter++</button>
  <button @click="asyncAddCounterHandle">异步增加</button>
</template>

<script>
import {mapState} from 'vuex';
import {mapGetters,mapMutations,mapActions} from 'vuex';

export default{
  computed:{
    ...mapGetters(["getCounter"]),
    ...mapState(["counter"])
  },
  methods:{
    ...mapMutations(["addCounter"]),
    ...mapActions(["asyncAddCounter"]),
    addCounterHandle(){
      this.addCounter(15)
    },
    asyncAddCounterHandle(){
      this.asyncAddCounter()
    }
  }
}
</script>
```

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/vuex-action.png)

