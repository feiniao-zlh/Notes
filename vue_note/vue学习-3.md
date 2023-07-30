### vue3新特性1

#### 组合API

`ref`或者`reactive`

```vue
<template>
  <h3>HelloWorld</h3>
  <div>
    <p>{{ message }}</p>
  </div>
  <ul>
    <!-- 注意names是一个对象,里面的list才是数据 -->
    <li v-for="(item,index) in names.list2" :key="index">{{ item }}</li>
  </ul>
</template>

<script>
import {ref,reactive} from 'vue';

export default {
  name: 'HelloWorld',
  // 组合式API
  setup(){
    // ref 基本类型
    const message = ref("一个消息")
    // reactive一般申明对象或者数组 复杂类型
    const names=reactive({
      list1:["1","a","b"],
      list2:["2","a","b"],
    })

    // return出去
    return{
      message,names
    }
  }
}
</script>
```

![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/ref%E5%92%8Creactive.png)

`methods`中的方法也可以放在`setup`里面

```vue
 	<button @click="clickHandle">按钮</button>
	// 方法
    // 必须通过message.value方法修改数据
    function clickHandle(){
      message.value = "111"
    }
```



`setup()`中使用`props`和`context`

`props`：组件传递数据

```vue
<template>
  <h3>HelloWorld</h3>

  <p>{{ msg }}</p>
</template>

<script>
export default {
  name: 'HelloWorld',
  props:{
    msg:{
      type:String
    }
  },

  setup(props){
    console.log(props.msg);
    const msg = props.msg;
    return{
      msg
    }
  }
}
</script>
```

#### context

`context`(简写`ctx`)：当前对象

在`setup`中没有`this`关键字，只有通过`ctx`获取实例对象

```vue
<template>
  <h3>HelloWorld</h3>
</template>

<script>
export default {
  name: 'HelloWorld',
  setup(ctx){
    console.log(this);
    console.log(ctx);
  }
}
</script>
```



![](https://raw.githubusercontent.com/feiniao-zlh/my_imgs/main/learn_vue/ctx.png)



### vue3新特性2

在`setup`中使用生命周期函数

```vue
<template>
</template>

<script>
import { onMounted } from 'vue';

export default {
  name: 'HelloWorld',
  setup(){
    // vue3下可以存在多个生命周期函数，方便后续使用时根据事务进行划分，不用所有事物写在一个函数里
    // 注意写法
    onMounted(()=>{
      console.log("生命周期函数1");
    }),
    onMounted(()=>{
      console.log("生命周期函数2");
    })
  }
}
</script>
```

