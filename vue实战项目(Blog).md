

## **vue实战项目(前后端分离) **

### 1.环境

![image-20201205095351203](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201205095351203.png)



1.1 node升降级

mac下

![image-20201205095655816](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201205095655816.png)

n --version

n 查看所有的版本

windows下重新覆盖即可



mac 配置环境变量

```shell
chenqipeng@chenqipengdeMacBook-Pro Imooc % cd ~
chenqipeng@chenqipengdeMacBook-Pro ~ % cat .bash_profile
chenqipeng@chenqipengdeMacBook-Pro ~ % echo $PATH
/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Library/Apple/usr/bin
```

![image-20201205133235380](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201205133235380.png)





linux 配置git环境变量（建立软连接）

![image-20201205112708624](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201205112708624.png)





![image-20201205140946017](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201205140946017.png)





2.跨域

> 前端服务和后端接口在不同的服务器上。



![image-20201207135437912](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201207135437912.png)



1. 修改响应头

![image-20201207140125440](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201207140125440.png)

​       



前端响应设置这个允许携带cookie

![image-20201207142520959](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201207142520959.png)

  

 

1. jsonp方式

   > 我们通过src href 请求的资源如img 是不存在跨域问题的
   >
   > 只有ajax请求才存在跨域问题
   >
   > jsonp就是基于这个原理





* CORS跨域   服务端设置 前端直接调用

* JSONP跨域   前端适配 后台配合

* 代理跨域








### **2.login 登录处理**



> Login.vue

```html
<template>
  <div>
    <el-container>
      <el-header>
        <img class="logo" src="../assets/pikachu.jpeg"/>
      </el-header>
      <el-main>
        <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
          <el-form-item label="用户名" prop="username">
            <el-input v-model="ruleForm.username"></el-input>
          </el-form-item>

          <el-form-item label="密码" prop="password">
            <el-input type="password" v-model="ruleForm.password"></el-input>
          </el-form-item>

          <el-form-item>
            <el-button type="primary" @click="submitForm('ruleForm')">立即创建</el-button>
            <el-button @click="resetForm('ruleForm')">重置</el-button>
          </el-form-item>
        </el-form>
      </el-main>
    </el-container>
  </div>
</template>

<script>
export default {
  name: "Login",
  data() {
    return {
      ruleForm: {
        username: 'markerhub',
        password: '111111',

      },
      rules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          { min: 3, max: 15, message: '长度在 3 到 15 个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'change' }
        ]
      }
    };
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          alert('submit!');
          // 因为调用了 axios 后 this 的地址是请求的地址，所以为了把数据存起来就要先预存当前的项目地址
          const _this = this
          this.$axios.post('http://localhost:8081/login',this.ruleForm).then(res =>{
            // console.log(res.headers);
            // console.log(res);
            const jwt = res.headers['authorization']
            const userInfo = res.data.data

            console.log(userInfo)

            // 把数据共享出去
            _this.$store.commit("SET_TOKEN",jwt)
            _this.$store.commit("SET_USERINFO",userInfo)
            // 获取
            console.log(_this.$store.getters.getUser)
          })
        } else {
          console.log('error submit!!');
          return false;
        }
      });
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    }
  }
}
</script>


<style scoped>
.el-header, .el-footer {
  background-color: #B3C0D1;
  color: #333;
  text-align: center;
  line-height: 60px;
}

.el-aside {
  background-color: #D3DCE6;
  color: #333;
  text-align: center;
  line-height: 200px;
}

.el-main {
  /*background-color: #E9EEF3;*/
  color: #333;
  text-align: center;
  line-height: 160px;
}

body > .el-container {
  margin-bottom: 40px;
}

.el-container:nth-child(5) .el-aside,
.el-container:nth-child(6) .el-aside {
  line-height: 260px;
}

.el-container:nth-child(7) .el-aside {
  line-height: 320px;
}
.logo{
  height: 80%;
  margin-top: 10px;
}
.demo-ruleForm{
  max-width: 500px;
  margin: 0 auto;
}
</style>
```



> store  index.js

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {
    // 属性
    token: '',
    userInfo: JSON.parse(sessionStorage.getItem("userInfo"))    // 反序列化
  },
  mutations: {
    // 相当于javabean 的set方法
    SET_TOKEN: (state,token) =>{
       state.token = token
       // 存到localstore时 浏览器关闭了 token还在
      localStorage.setItem("token",token)
     },
    SET_USERINFO: (state,userInfo) => {
      state.userInfo = userInfo
      // 不能存对象 我们需要序列化
      sessionStorage.setItem("userInfo",JSON.stringify(userInfo))
    },
    REMOVE_INFO: (state) =>{
      state.token = ''
      state.userInfo = {}
      localStorage.setItem("token",'')
      sessionStorage.setItem("userInfo",JSON.stringify(''))
    }

  },
  getters:{
   // get
    getUser: state => {
      return state.userInfo
    }
  },
  actions: {
  },
  modules: {
  }
})
```











```js
import axios from 'axios'
import Element from 'element-ui'
import router from './router'
import store from './store'
axios.defaults.baseURL="http://localhost:8081"

axios.interceptors.request.use(config =>{
    console.log("前置拦截器前")
    return config
})

axios.interceptors.response.use(response => {
    let res = response.data;
    console.log("==========")
    console.log(res)
    console.log("==========")

    if (res.code === 200) {
        return response
    } else {
        // this.$message.error('错了哦，这是一条错误消息');
        // 这里不能直接使用 elementui 官网的代码 因为我们这个axios.js是挂靠在main.js下的
        // 所以我们要重新引入elementui
        Element.Message.error('错了哦，这是一条错误消息', {duration: 3 * 1000})    // 三秒后自动消失
        //  阻止进入后面的逻辑 reject
        return Promise.reject(response.data.msg)
    }
},error => {
    console.log(error.response)
    if(error.response.data){
        error.message = error.response.data.msg
    }
    if(error.response.status === 401){
      store.commit("REMOVE_INFO")
    router.push("/login")
    }
    Element.Message.error(error.message, {duration: 3 * 1000})
    return Promise.reject(error)
    },

)
```





### 3.接口数据处理

![image-20201230150809935](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201230150809935.png)



java后端

 @RequestParam(defaultValue = "2") Integer currentPage  
get请求中 有参数 currentPage的话就是参数的值

get请求中没有currentPage 的话就是 defaultValue的值 这里为2

```java

@GetMapping("/blogs")
 public Result list(@RequestParam(defaultValue = "2") Integer currentPage){
    Page page = new Page(currentPage,5);
    IPage pageData = blogService.page(page, new QueryWrapper<Blog>().orderByDesc("created"));
    return Result.succ(pageData);
 }
```



前端

```js
methods:{
    page(currentPage){
     const _this = this
     _this.$axios.get("/blogs?currentPage="+currentPage).then(res =>{
       console.log("====获得blogs====")
       console.log(res)
     })
    }
  },
  created() {
    this.page(1)
  }
}
```



![image-20201230152343124](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201230152343124.png)

![image-20201230152401214](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201230152401214.png)





### 4.jwt



登录只是为了拿到 jwt 生成的 token ，这个 token 就是一个权限令牌。以后访问需要被保护的接口时，请求头带上你之前登录拿到的 token，服务端做验证。 jwt 是生成 token 的。 token 是访问受限制资源被要求提供的。

1. 登录拿 token
2. 验证