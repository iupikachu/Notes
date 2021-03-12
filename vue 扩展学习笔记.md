





## vue 扩展学习笔记

### 1.vuex

Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式**。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。Vuex 也集成到 Vue 的官方调试工具 [devtools extension (opens new window)](https://github.com/vuejs/vue-devtools)，提供了诸如零配置的 time-travel 调试、状态快照导入导出等高级调试功能。

> vuex是vue配套的公共数据管理工具，我们可以将共享的数据保存到vuex中

#### 1.1 vuex数据共享

> 导入vuex必须先导入vue
>
> state就相当于组件中的data ,就是专门用于保存共享数据的

1.创建vuex对象，在state里面存共享的数据  在vue实例上面

```js
//创建vuex对象
const store = new Vuex.Store({
    state:{
        msg:"这是共享的数据"
    },
});
```

2.在父组件引入vuex创建的实例 只要父组件有了store其下的子组件也可以用

![image-20201201154128134](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201201154128134.png)

3.在template里面直接使用即可，不过格式有要求

```js
<p>{{this.$store.state.msg}}</p>
```

4. 操作共享数据

>  注意点: 在vuex中不推荐直接修改共享数据
>        如果多个组件都修改了共享的数据，那么后期数据发生了错误，如果我们需要去调试错误
>       就需要把每一个修改了共享数据的组件都检查一遍，这样非常低效，不利于维护



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="js/vue.js"></script>
    <script src="js/vuex.js"></script>
</head>
<body>
<div id="app">
    <father></father>
</div>
</body>
<template id="father">
    <div>

        <son1></son1>
        <son2></son2>
    </div>
</template>
<template id="son1">
    <div>

        <button @click="add">增加</button>
        <button @click="sub">减少</button>
        <input type="text" :value="this.$store.state.count">
    </div>
</template>
<template id="son2">

    <div>
        <button @click="add">增加</button>
        <button @click="sub">减少</button>
        <input type="text" :value="this.$store.state.count">
    </div>
</template>
<script>
    //创建vuex对象
    const store = new Vuex.Store({
        // 保存共享数据
        state: {
            count: 0
        },
        // 保存修改共享数据的方法
        mutations: {
            mAdd(state) {
                state.count = state.count + 1;
            },
            mSub(state) {
                state.count = state.count - 1;
            }
        }
    });
    //爸爸组件
    Vue.component("father", {
        template: `#father`,
        store: store,

        //儿子组件
        components: {
            "son1": {
                template: `#son1`,
                methods: {
                    add() {
                        // 注意点: 在vuex中不推荐直接修改共享数据
                        // 如果多个组件都修改了共享的数据，那么后期数据发生了错误，如果我们需要去调试错误
                        // 就需要把每一个修改了共享数据的组件都检查一遍，这样非常低效，不利于维护
                        // this.$store.state.count = this.$store.state.count + 1;
                        this.$store.commit("mAdd")
                    },
                    sub() {
                        // this.$store.state.count = this.$store.state.count - 1;
                        this.$store.commit("mSub")
                    },
                }
            },
            "son2": {
                template: `#son2`,
                methods: {
                    add() {
                    
                        this.$store.commit("mAdd")
                    },
                    sub() {
                        // this.$store.state.count = this.$store.state.count - 1;
                        this.$store.commit("mSub")
                    },
                }
            }
        }
    })


    let vue = new Vue({
        el: '#app',
        data: {},
        methods: {}
    });
</script>
</html>
```



5. vuex getters属性

> getters 类似于 vue的computed 计算属性 (将计算结果缓存起来，只要数据不发生变化。只会计算一次)

1. vuex实例中

```js
getters:{
    formart(state){
       return state.msg + "getters只计算一次拼接的字符串"
    }
}
```

```js
{{this.$store.getters.formart}}
```

如果想缓存数据的话，在组件中用computed,在vuex中用getters缓存



### 2.vueRouter

#### 2.1 切换组件

> vueRouter用来切换组件显示的
>
> v-if v-show 通过标记来切换(true/false)
>
> vueRouter 通过哈希（hash）来切换  vueRouter还能够在切换的时候传递参数
>
> 注意点: 和vuex一样先导入vue 再导入vue-router

![image-20201201164035300](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201201164035300.png)

这个#后面的就是hash值

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="js/vue.js"></script>
    <script src="js/vue-router.js"></script>

</head>
<body>
   <div id="app">
          <a href="#/one">切换到第一个界面</a>
          <a href="#/two">切换到第二个界面</a>
<!--       路由出口-->
<!--       路由匹配到的组件将渲染在这里-->
          <router-view></router-view>
   </div>
</body>
<template id="one">
    <div class="onepage">
        <p>我是第一个界面</p>
    </div>
</template>
<template id="two">
    <div class="twopage">
        <p>我是第二个界面</p>
    </div>
</template>
<script>
    // 1.定义组件
    const one ={
        template:`#one`
    };

    const two ={
        template:`#two`
    };
    // 2. 定义切换的规则(定义路由规则)
    const routes=[
        //数组中的每一个对象就是一条规则
        {path:'/one',component: one},
        {path:'/two',component: two}
    ];
    // 3.根据自定义的切换规则创建路由对象
    const router = new VueRouter({
        routes: routes
    });
    let vue =new Vue({
        el: '#app',
        // 4.将创建好的路由对象绑定到Vue实例上
        router: router,
        data: {

        },
        methods:{

        },
        computed:{

        },
        components:{
             one: one,
             two: two,
        }
    });
</script>
</html>
```



**用router-link代替a标签   不用写#了**

> 默认情况下vue在渲染router-link的时候，是通过a标签渲染的
>
> 如果不想用a标签渲染，那么可以tag属性来指定



```html
<router-link to="/one" tag="button">切换到第一个界面</router-link>
<router-link to="/two">切换到第二个界面</router-link>
<router-view></router-view>
```



![image-20201202141507159](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201202141507159.png)



添加激活状态的显示

```html
<style>
    .router-link-active{
        background: red;
    }
```

![image-20201202141721516](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201202141721516.png)

也可以用linkActiveClass去指定 激活class的名字

![image-20201202142039666](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201202142039666.png)



利用重定向实现默认页面

```js
{path:'/',redirect:'/one'},
```



#### 2.2 参数传递

> 只要将vue router 挂载到了 Vue的实例对象上，我们就可以通过 vue.$route 拿到路由对象
>
> 只要拿到路由对象，就可以通过路由对象拿到传递的参数
>
> 方式一: 通过url参数传参  ?key=value&key=value  在组件的生命周期方法中通过this.$route.query获取
>
> 方式二: 通过占位符传递    路由规则中  /:key/:key    路径中 /value/value 通过 this.$route.params 获取
>
> 





```js
<router-link to="/one?name=cqp&age=33" tag="button">切换到第一个界面</router-link>
<router-link to="/two/cqp/24">切换到第二个界面</router-link>
<router-view></router-view>
```



```js
const routes=[
    {path:'/',redirect:'/one'},
    //数组中的每一个对象就是一条规则
    {path:'/one',component: one},
    {path:'/two/:name/:age',component: two}
];
```



```js
const one ={
    template:`#one`,
    created: function (){
        console.log(this.$route);
        console.log(this.$route.query.name);
        console.log(this.$route.query.age);
    }
};

const two ={
    template:`#two`,
    created: function (){
        console.log(this.$route);
        console.log(this.$route.params.name);
        console.log(this.$route.params.age);
    }
};
```



![image-20201202143858575](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201202143858575.png)





![image-20201202144347709](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201202144347709.png)





#### 2.3 嵌套路由

> 又称为子路由，在被切换的组件中又切换其他子组件



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="js/vue.js"></script>
    <script src="js/vue-router.js"></script>
    <style>
      *{
          margin: 0;
          padding: 0;
      }
      .onepage{
          height: 500px;
          width: 500px;
          background: skyblue;
      }
      .twopage{
          height: 500px;
          width: 500px;
          background: cornflowerblue;
      }
      .onesub1page{
          height: 200px;
          width: 200px;
          background: chocolate;
      }
      .onesub2page{
          height: 200px;
          width: 200px;
          background: beige;
      }
    </style>
</head>
<body>
<div id="app">
        <router-link to="/one" tag="button">切换到第一个界面</router-link>
        <router-link to="/two" tag="button">切换到第二个界面</router-link>

        <router-view></router-view>
</div>
</body>
<template id="one">
    <div class="onepage">
        <p>我是第一个页面</p>
        <router-link to="/one/onesub1" tag="button">切换到第一个子页面</router-link>
        <router-link to="/one/onesub2" tag="button">切换到第二个子页面</router-link>
        <router-view></router-view>
    </div>
</template>
<template id="onesub1">
    <div class="onesub1page">
        <p>我是第一个界面子界面1</p>
    </div>
</template>
<template id="onesub2">
    <div class="onesub2page">
        <p>我是第一个界面子界面2</p>
    </div>
</template>
<template id="two">
    <div class="twopage">
        <p>我是第二个界面</p>
    </div>
</template>
<script>
    const onesub1 ={
        template: `#onesub1`,
    };
    const onesub2 ={
        template: `#onesub2`,
    };

    const one = {
        template:`#one`,
        components: {
            onesub1:onesub1,
            onesub2:onesub2
        }
    };
    const two ={
        template:`#two`
    };

    const routes =[
        {
            path:'/one',
            component:one,
            children:[
                {
                    //注意点: 如果是嵌套路由（子路由），那么不用写一级路径的地址，并且不用写/
                    path:"onesub1",
                    component: onesub1
                },
                {
                    //注意点: 如果是嵌套路由（子路由），那么不用写一级路径的地址，并且不用写/
                    path:"onesub2",
                    component: onesub2
                }
            ]
        },
        {path:'/two',component:two},
        // {path:'/one/onesub1',component: onesub1},
        // {path:'/one/onesub2',component: onesub2},
    ];

    const router = new VueRouter({
        routes: routes,
        // linkActiveClass:"cqp-active"
    });

    let vue =new Vue({
        el: '#app',
        router:router,
        data: {

        },
        methods:{

        },
        computed:{

        },
        components:{
             one:one,
             two:two,
        }
    });
</script>
</html>
```

![image-20201202164222491](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201202164222491.png)



#### 2.4 命名视图

> 命名视图和具名插槽很像，都是让不同的出口显示不同的内容
>
> 命名视图就是当路由地址被匹配的时候同时指定多个出口，并且每个出口显示的内容不同

![image-20201202170054573](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201202170054573.png)

![image-20201202170117710](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201202170117710.png)

![image-20201202170129093](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201202170129093.png)





### 3.watch属性

> 实时监听数据，很消耗浏览器性能
>
> 只要数据发生变化就会自动调用watch的方法

简易计算器

![image-20201202192311527](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201202192311527.png)

![image-20201202192332292](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201202192332292.png)

![image-20201202192348024](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201202192348024.png)





> **watch 也可以监听路由的变化**
>
> 只要路由地址发生了变化机会自动调用对应的回调函数

```js
watch:{
    "$route.path": function (newValue,oldValue){
        console.log(newValue,oldValue);
    }
},
```

![image-20201202192956497](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201202192956497.png)



### 4.vue生命周期



1.创建期间的生命周期方法:

* beforeCreate    没数据

* created           有数据了

* beforeMount    有模板没渲染

* mounted         渲染了

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
      <script src="js/vue.js"></script>
      <script src="js/vuex.js"></script>
      <script src="js/vue-router.js"></script>
  </head>
  <body>
  <div id="app">
      <p>{{msg}}</p>
  </div>
  </body>
  <template >
  </template>
  <script>
  
  
      let vue =new Vue({
          el: '#app',
          beforeCreate:function (){
              /*
              在调用beforeCreate的时候。仅仅表示vue实例刚刚被创建出来
              在此时此刻还没有初始化好Vue实例中的数据和方法，所以此时此刻还不能访问vue实例中保存的数据和方法。
               */
             // console.log(this.msg);
             // console.log(this.say);
          },
          created:function (){
              /*
              这个时候数据和方法初始化好了
              这里是我们最早可以访问vue实例中保存的数据和方法的地方
               */
              console.log(this.msg);
              console.log(this.say);
          },
          beforeMount:function (){
            /*
           开始编译模板 根据data中的数据和指令生成html，但是并不会渲染到页面上，还在内存中
           <div id="app">
                       <p>cqp</p>
           </div>
             */
           // console.log(document.querySelector("p").innerHTML);
           // console.log(document.querySelector("p").innerText);
          },
          mounted:function (){
              /*
              在调用mounted的时候，表示vue已经完成了模板的渲染，表示我们可以拿到界面上渲染之后的内容了
               */
              console.log(document.querySelector("p").innerHTML);
              console.log(document.querySelector("p").innerText);
          },
          data: {
              msg:"cqp"
          },
          methods:{
              say(){
                  console.log("say");
              }
          },
          computed:{
  
          },
          components:{
  
          }
      });
  </script>
  </html>
  ```

2.运行期间的生命周期方法

* beforeUpdate
* updated

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="js/vue.js"></script>
    <script src="js/vuex.js"></script>
    <script src="js/vue-router.js"></script>
</head>
<body>
<div id="app">
  <p>{{msg}}</p>
</div>
</body>
<template >
</template>
<script>
    let vue =new Vue({
        beforeUpdate:function (){
            /*
            Vue实例中保存的数据被修改了，但是界面还未更新
             */
            console.log(document.querySelector("p").innerHTML);
        },
        updated:function (){
            /*
            调用updated时候，vue实例中保存的数据被修改了，并且界面也同步了修改的数据了
            也就是说：数据和界面都同步更新之后就会调用 updated 方法
             */
            console.log(document.querySelector("p").innerHTML);
        },
        el: '#app',

        data: {
             msg:"cqp",
        },
    });
</script>
</html>
```

3.销毁期间的生命周期方法

* beforeDestroy
* destroyed

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="js/vue.js"></script>
    <script src="js/vuex.js"></script>
    <script src="js/vue-router.js"></script>
</head>
<body>
<!--  Vue实例对象可以看作是一个大的组件，我们自定义的组件可以看作是一个小的组件

-->
<div id="app">
    <button @click="change">切换</button>
<!--    通过v-if来切换，会直接删除和重新创建-->
    <one v-if="isShow"></one>
</div>
</body>
<template id="one">
    <div>
        <p>我是组件</p>
    </div>
</template>
<script>
   Vue.component("one",{
       template:`#one`,
       beforeDestroy: function (){
           /*
           只要组件不被销毁，那么beforeDestroy就不会调用
           beforeDestroy函数是我们最后能够访问到组件数据和方法的函数
            */
           console.log("beforedestory");
           console.log(this.msg);
           console.log(this.say)
       },
       destroyed:function (){
          /*
          在调用destroyed的时候，表示当前组件已经被销毁了
          不要在这个生命周期方法中再去操作组件中的数据和方法
           */
       },
       data:function (){
           return {
               msg:"cqp",
           }
       },
       methods:{
           say(){
               console.log("say")
           }
       }
   });

    let vue =new Vue({
        el: '#app',

        data: {
              isShow:true,
        },
        methods:{
              change(){
                  this.isShow = !this.isShow;
              }
        },
    });
</script>
</html>
```

https://cn.vuejs.org/v2/guide/instance.html#%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E5%9B%BE%E7%A4%BA

> mounted之后会实时监听数据，数据如果发生变化会调用beforeUpdate



### 5.  vue特殊特性

> ### vue特点:
>
> ​               数据驱动界面更,无需操作dom来更新界面
>
> ​               vue不推荐我们直接操作dom，但是有时候确实需要拿到dom去操作
>
> ​               方法: 我们可以通过 ref 来获取

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="js/vue.js"></script>
    <script src="js/vuex.js"></script>
    <script src="js/vue-router.js"></script>
</head>
<body>
<div id="app">
    <button @click="myFn">我是按钮</button>
    <p ref="myp">我是原生的DOM</p>
    <one id="myOne" ref="myOne"></one>

</div>
</body>
<template id="one">
    <div>
        <p>我是组件</p>
    </div>
</template>
<script>
   Vue.component("one",{
       template:`#one`,
       data: function (){
           return {
               msg:"cqp"
           }

       },
       methods: {
           say(){
               console.log("say");
           }
       }
   })

    let vue =new Vue({
        el: '#app',

        data: {

        },
        methods:{
            myFn(){
                /*
                注意点: 如果是通过这个原生的语法来获取元素，无论是原生的元素还是自定义的组件，拿到的都是原生的元素
                       并且vue官方不推荐我们这样获取

                console.log(document.querySelector("p"));
                console.log(document.querySelector("#myOne"));
                 */

                /*
                   注意点：在vue中如果想获取原生的元素或自定义的组件，可以通过ref来获取
                          如果是添加给原生的元素，那么拿到的就是原生的元素
                          如果是添加给自定义的组件，那么拿到的就是自定义的组件
                 */
                  console.log(this.$refs);
                  console.log(this.$refs.myp);
                console.log(this.$refs.myOne);
                console.log(this.$refs.myOne.msg);
                console.log(this.$refs.myOne.say);
            }
        },
        computed:{

        },
        components:{

        }
    });
</script>
</html>
```

![image-20201203142608518](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201203142608518.png)



### 6.vue-cli (command line interface)



> Vue-Cli 是官方提供的脚手架
>
> 默认帮我们搭建好了一套利用 webpack 管理的vue的项目结果



![image-20201203154101698](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201203154101698.png)



public 文件夹不经过webpack，打包后直接复制到dist目录。需要绝对路劲来引用它们。

一般用于存储一些永远不会改变的静态资源或者webpack不支持的第三方库。

asserts 文件存的静态资源会经过webpack处理（压缩） 

![image-20201203154712639](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201203154712639.png)



> 运行项目   npm run serve
>
> 打包项目  npm run build





index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>第一个通过vue-cli开发的项目</title>
</head>
<body>
<div id="app">

</div>
</body>
</html>
```

app.vue

```html
<template>
     <div id="app">
     <p>{{msg}}</p>
       <button @click="say">我是按钮</button>
     </div>
</template>

<script>
export default {
   name: "App",
  data: function (){
     return{
       msg: "cqasdfsdfsasp",
     }
  },
  methods:{
     say(){
       console.log("say")
     }
  }
}
</script>

<style scoped>
p{
  background: aqua;
}
</style>
```

main.js

```js
import Vue from 'vue'
import App from './App.vue'
new Vue({
    el:'#app',
    render: b => b(App)
});
```



> #### 修改webpack配置



默认情况下通过vue-cli创建的项目已经自动给我们配置好了webpack

但是有时候默认的配置可能不能满足我们的需求，例如我们想修改输出目录名称，想增加一些插件。但是Vue-cli创建的项目里又没有webpack配置文件，那么我们应该如何修改或增加webpack配置?

方法:

​       新建vue.config.js 在其中的configureWebpack属性来新增webpack配置





```js
const webpack = require('webpack');
module.exports ={



    /*
    vue-cli 为了方便起见对webpack原有的属性进行了一层封装，如果我们需要修改webpack的配置
    那么我们可以在项目中新建一个vue.config.js的文件，然后去查询Vue-Cli的封装能否满足我们的需求
    如果可以满足，那么就是用vue-cli封装的属性来修改webpack的配置
    如果不可以满足我们的需求，那么我们可以通过configureWebpack的属性来编写原生的webpack配置
     */
    outputDir:'bundle',
    configureWebpack:{
        //可以在这个对象中编写原生的webpack配置
        plugins:[
            new webpack.BannerPlugin({
                banner:'cqp-www.cqp.com'
            })
        ]
}


}
```





![image-20201203202555299](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201203202555299.png)



### 7.vue插件（plugin）



> 把组件封装成一个插件

​          loading这样引入为局部组件

![image-20201204093411019](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201204093411019.png)

全局组件

![image-20201204093729658](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201204093729658.png)





第三种方式  通过vue.use(Loading) Loading作为一个插件

Vue.js 的插件应该暴露一个 `install` 方法。

在src目录下新建 plugin文件夹

![image-20201204094629119](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201204094629119.png)

```js
import Vue from 'vue'
import Loading from './Loading'

export default {
    install: function (){
        Vue.component(Loading.name,Loading)
    }
}
```

![image-20201204094918738](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201204094918738.png)





不用install插件代码手动添加组件到界面上

```js
import Vue from 'vue'
import Loading from './Loading'

export default {
    // 注意点：如果要讲一个组价封装成一个插件，那么必须提供一个install方法
    //        那么必须在install方法中注册当前的这个组件
    install: function (){
        // Vue.component(Loading.name,Loading)

        // 1.根据我们的组件生成一个构造函数
        let LoadingContructor =Vue.extend(Loading)
        // 2.根据构造函数创建实例对象
        let LoadingInstance =new LoadingContructor()
        // 3.随便创建一个标签(元素)
        let oDiv = document.createElement('div')
        // 4. 将创建好的标签添加到界面上
        document.body.appendChild(oDiv)
        // 5.将创建好的实例对象挂载到创建好的元素上
        LoadingInstance.$mount(oDiv)
    }
}
```





插件传参





![image-20201204103709457](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201204103709457.png)



```js
import Loading from './Loading'

export default {
    // 注意点：如果要讲一个组价封装成一个插件，那么必须提供一个install方法
    //        那么必须在install方法中注册当前的这个组件
    install: function (Vue,Options){
        // Vue.component(Loading.name,Loading)

        // 1.根据我们的组件生成一个构造函数
        let LoadingContructor =Vue.extend(Loading)
        // 2.根据构造函数创建实例对象
        let LoadingInstance =new LoadingContructor()
        // 3.随便创建一个标签(元素)
        let oDiv = document.createElement('div')
        // 4. 将创建好的标签添加到界面上
        document.body.appendChild(oDiv)
        // 5.将创建好的实例对象挂载到创建好的元素上
        LoadingInstance.$mount(oDiv)
        if(Options && Options.title !== null && Options.title !== undefined ){
            LoadingInstance.title = Options.title
        }
    }
}
```

![image-20201204103723185](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201204103723185.png)

插件通常用来为 Vue 添加全局功能。插件的功能范围没有严格的限制——一般有下面几种：

1. 添加全局方法或者 property。如：[vue-custom-element](https://github.com/karol-f/vue-custom-element)
2. 添加全局资源：指令/过滤器/过渡等。如 [vue-touch](https://github.com/vuejs/vue-touch)
3. 通过全局混入来添加一些组件选项。如 [vue-router](https://github.com/vuejs/vue-router)
4. 添加 Vue 实例方法，通过把它们添加到 `Vue.prototype` 上实现。
5. 一个库，提供自己的 API，同时提供上面提到的一个或多个功能。如 [vue-router](https://github.com/vuejs/vue-router)





**把组件封装成插件是为了方便对其额外扩展**

