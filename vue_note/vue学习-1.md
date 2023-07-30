### vue创建项目

```java
// 创建项目
vue create 项目名称
// 手动管理 选择1,3和router 版本3 	no	 config files	  no
// 进入项目
cd 项目名称
//开启服务
npm run server
```



### v-if 和v-show区别

```VUE
<!-- v-if v-show -->

<!-- v-if是直接连html的框架都没有了而v-show只是改变了css，所以如果内容要频繁的改变的话，v-if会很浪费资源 -->

<template>
  <div class="hello">
    <p v-if="flage">v-if</p>
     <!-- <p v-else>v-else</p> -->
    <p v-show="flage">v-show</p> 
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data(){ 
    return{
      flage:false
    }
  },
}
</script> 
```

`flag:true`时可以看见`if`的<p>标签

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/v-if_show_true.png)

`flag:false`是则无

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/v-if_%20show_false.png)

### 循环

```vue
<template>
  <div class="hello">
    <!-- 循环 -->
    <ul>
      <!-- 格式为v-for="item in items-->
      <!-- 此处item为arrays里第一个花括号包括花括号的所有内容，items为数据存储容器=arrays-->
      <!-- 以index默认索引为键检查是否更新数据，即arrays内容是否改变，若改变只渲染改变的数据，从而减少资源消耗 -->
      <!-- 一般以itme.id为属性进行检查 -->
      <li v-for="(item,index) in arrays" key="index">
        {{ item.message }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data(){ 
    return{
      arrays:[
        {
          id:1,
          message:"消息1"
        },
        {
          id:2,
          message:"消息2"
        }
      ]
    }
  },
}
</script> 
```

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/%E5%BE%AA%E7%8E%AF.png)



### 事件处理

```vue
<template>
  <div class="hello">
    
    <!-- 事件触发 -->
    <!-- v-on:click = @click  -->
    <button @click="counter+=1">点击事件：{{ counter }}</button>
    <button @click="handle">归零{{ counter }}</button>
    <button @click="counterHandle('参数1')">1事件：{{ message }}</button>
    <button @click="counterHandle('参数2')">2事件：{{ message }}</button>
    
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data(){ 
    return{
      message:"默认值",
      counter:0
    }
  },
  methods:{
    handle(){
      this.counter = 0
    },
    counterHandle(data){
      this.message=data
    }

  }
}
</script> 
```

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/%E4%BA%8B%E4%BB%B6%E5%A4%84%E7%90%86.png)

### 双向数据传输

```vue
<template>
  <!-- npm run serve -->
  <div class="hello">
    <!-- 实时获取用户输入 -->
    <input type="text" v-model="username">
    <!-- 回车获取用户输入 -->
    <input type="text" v-model.lazy="password">
    <!-- 自动去除输入首位空格 -->
    <input type="text" v-model.trim="username">
    <p>{{ username }}</p>
    <p>{{ password }}</p>

  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data(){ 
    return{
      username:"",
      password:""
    }
  },
  methods:{
  }
}
</script> 
```

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/%E5%8F%8C%E5%90%91%E6%95%B0%E6%8D%AE%E4%BC%A0%E8%BE%93.png)

### 组件基础

`MyCompoent`

```vue
<!-- 模板 -->
<template>
    <h3>单文件组件</h3>

</template>

<!-- 属性 -->
<script>
export default{
    name:"MyComponent"
}
</script>

<!-- 样式 -->
<!-- scoped:如果在style中添加此属性，代表当前样式只在当前组件中生效 -->
<style scoped>
h3{
    color: red;
}
</style>
```

添加组件，在根组件`App.vue`下添加

```vue
	<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <HelloWorld msg="Welcome to Your Vue.js App"/>

  <!-- 引用 -->
  <MyComponent></MyComponent>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

// 引入
import MyComponent from './components/MyComponent.vue';

export default {
  name: 'App',
  components: {
    HelloWorld,
    // 引入
    MyComponent
  }
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

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/%E7%BB%84%E4%BB%B6.png)

**注：**不是一定要在`App.vue`下添加，也可以在其他组件中添加。

例如在'黑色'组件中有两个红色组件，第二个红色有三个蓝色组件，形成一棵树的形式，这样修改的时候就找到相应组件修改即可

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/%E7%BB%84%E4%BB%B6%E6%A0%91.png)

### 组件间数据传输

`App.vue`下传递数据

```vue
<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <HelloWorld msg="Welcome to Your Vue.js App"/>
  <!-- 数据传递 :进行命名传递 空格隔开 -->
  <MyComponent :names="names" :number="number" :text="text"></MyComponent>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import MyComponent from './components/MyComponent.vue';

export default {
  name: 'App',
// 创造数据
  data(){
    return{
      names:["a","b","c"],
      number:1,
      text:"文本"
    }
  },
  components: {
    HelloWorld,
    MyComponent
  }
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

`MyComponent`接收数据

```vue
<template>
    <h3>组件间数据传输</h3>
    <p>number = {{ number }}</p>
    <p>text = {{ text }}</p>

    <ul>
        <li v-for="(item, index) in names" :key="index">{{ item }}</li>
    </ul>
</template>

<script>
export default{
    name:"MyComponent",
    // 模板 从父组件传递到子组件 若要反向传递需要使用自定义事件组件交互
    props:{
        // 每一个参数写明类型和默认值

        // 数组和对象必须使用函数进行返回
        names:{
            type:Array,
            default:function(){
                return []
            }
        },
        number:{
            type:Number,
            default:0
        },
        text:{
            type:String,
            default:""
        }

    }
}
</script>

<style scoped>
</style>
```



![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/%E7%BB%84%E4%BB%B6%E9%97%B4%E6%95%B0%E6%8D%AE%E4%BC%A0%E8%BE%93.png)

### 自定义事件组件交互

`MyComponent`添加数据

```vue
<template>
    <h3>自定义事件组件交互</h3>

    <!-- 定义事件 -->
    <button @click="sendMessage">数据传递</button>

</template>

<script>
export default{
    name:"MyComponent",
    data(){
        return{
            message:"子组件数据"
        }
    },
    methods:{
        sendMessage(){
            // 模板
            // 参数1:字符串:理论上是随便的,但是需要具有意义
            // 参数2:传递的数据
            this.$emit("onEvent",this.message)
        }
    }
} 
</script>

<style scoped>
</style>
```

`App.vue`接收

```vue
<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <HelloWorld msg="Welcome to Your Vue.js App"/>

  <!-- 自定义事件传递数据 -->
  <MyComponent @onEvent="getData"></MyComponent>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import MyComponent from './components/MyComponent.vue';

export default {
  name: 'App',
  components: {
    HelloWorld,
    MyComponent
  },
  // 接收数据
  methods:{
    // data就是传来的数据
    getData(data){
      console.log(data)
    }
  }
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



![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BA%8B%E4%BB%B6%E4%BA%A4%E4%BA%92.png)

### ******* 组件生命周期

```vue
<template>
    <h3>组件生命周期</h3>
    <p>{{ message }}</p>
    <button @click="message = 更新数据">数据</button>

</template>

<script>
import { onUnmounted } from 'vue';

export default{
    name:"MyComponent",
    data(){
        return{
            message:""
        }
    },
    beforeCreate(){
        console.log("beforeCreate:组件创建之前");
    },
    created(){
        console.log("created:组件创建之后");
    },
    beforeMount(){
        console.log("beforeMount:组件渲染之前");
    },
    mounted(){
        console.log("mounted:组件渲染之后");
        // 网络请求放在这里
        // 例:网卡的时候淘宝会显示一个个小框框组件,但是框里面的内容要等会才能出现
    },
    beforeUpdate(){
        console.log("beforeUpdate:组件更新之前");
    },
    updated(){
        console.log("updated:组件更新之后");
    },
    beforeUnmount(){
        console.log("beforeUnmount:组件卸载之前");
        // 卸载之前,把消耗性能的处理删除掉
        // 例:定时器
    },
    unmounted(){
        console.log("unmounted:组件卸载之后");
    }


} 
</script>

<style scoped>
</style>
```

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/%E7%BB%84%E4%BB%B6%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.png)

### 引入第三方-swiper

[vue使用swiper]: https://swiperjs.com/vue

引包 `save`保留版本号，可以在`package.json`下找到

```vue
cnpm install --save swiper
```

```vue
<template>
  <div>
    <!-- 轮播图 -->
    <Swiper
    :modules="modules" 
    :pagination="{clickable:true}"
    >
      <SwiperSlide>
        <img src="../assets/logo.png"> 
      </SwiperSlide>
      <SwiperSlide>
        <img src="../assets/logo.png"> 
      </SwiperSlide>
      <SwiperSlide>
        <img src="../assets/logo.png"> 
      </SwiperSlide>
    </Swiper>
  </div>
</template>

<script>
// 引入指示器
import {Pagination} from 'swiper/modules';

// 引入swiper
import {Swiper, SwiperSlide} from 'swiper/vue';
import 'swiper/css';
import 'swiper/css/pagination';

export default {
  name: 'HelloWorld',
  data(){
    return{
      modules:[Pagination]
    }
  },
  // 引入组件
  components:{
    Swiper,
    SwiperSlide
  }
}
</script> 

<style scoped>
img{
  /* 图片设置大小 */
  /* width: 100%; */
}
</style>

```

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/swiper.png)