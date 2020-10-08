# 1 axios请求方式

![image-20201005220440919](C:\Users\周鹏飞\AppData\Roaming\Typora\typora-user-images\image-20201005220440919.png)

**在vue里安装axios**

```
npm install axios --save
```

# 2 axios基本使用请求数据

**代码如下**

```js
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'
import axios from 'axios'
Vue.config.productionTip = false

new Vue({
  router,
  store,
  render: h => h(App)
}).$mount('#app')

axios({
  url:'http://123.207.32.32:8000/home/multidata'//请求的网络链接
  method:'get'//不写默认为get请求
}).then(res=>{
  console.log(res); //可使用promise 上面就是返回一个promise函数，这里res接受返回结果并且打印
})
```

**运行如下**

![image-20201005222700834](https://gitee.com/zpfzpf123/axios-img/raw/master/image-20201005222700834.png)

**上述代码也可以这样写**

```js
axios.get('http://123.207.32.32:8000/home/multidata').then(res=>{
  console.log(res);
})
```

# 3 axios接受多个数据后再处理后面的业务

**要想达到这样的结果，我们需要使用axios.all函数**

```js
axios.all([(
    axios({
      url:'http://123.207.32.32:8000/home/multidata' //接受的第一个数据
    })
),(
    axios({
      url:'http://123.207.32.32:8000/home/multidata' //接受的第二个数据
    })
)]).then(res=>{
  console.log(res); //把接受到的数据打印出来 
})
```

**效果**

![image-20201006105400170](C:\Users\周鹏飞\AppData\Roaming\Typora\typora-user-images\image-20201006105400170.png)

****

**如果要对两个数据单独处理**

```js
axios.all([(
    axios({
      url:'http://123.207.32.32:8000/home/multidata'
    })
),(
    axios({
      url:'http://123.207.32.32:8000/home/multidata'
    })
)]).then(axios.spread((res1,res2)=>{//res1和res2为两个数据 后面跟上处理结果
  console.log(res1);
  console.log(res2);
}))
```

**处理结果**![image-20201006110158208](C:\Users\周鹏飞\AppData\Roaming\Typora\typora-user-images\image-20201006110158208.png)

# 4 axios的全局配置

> 在日常开发中，哼多时候有些参数我们都是固定的，没有必要重复写它，这个时候我们可以采取全局配置，

**如下**

```js
axios.defaults.baseURL='http://123.207.32.32:8000' //配置基础url
axios.defaults.timeout=5000 //配置响应时间

axios.all([(
    axios({
      url:'/home/multidata'
    })
),(
    axios({
      url:'/home/multidata'
    })
)]).then(axios.spread((res1,res2)=>{
  console.log(res1);
  console.log(res2);
}))
```

**还有很多全局配置，见官网 ,下面是一些常见实例**

![image-20201006112312231](C:\Users\周鹏飞\AppData\Roaming\Typora\typora-user-images\image-20201006112312231.png)

# 5 axios的实例和模块封装

 **在日常项目中，我们这样全局配置是不合理的，不是每个地方的url都是这个前缀，还有，如果我们把axios都写在main.js里也是不合理的，以后如果项目越来越复杂，我们需要在很多地方用到axios的话，写起来会很繁琐，而且不利于我们的维护，于是，我们最好把axios封装起来，方便我们后续使用**

> 我们创建一个 src/network/request.js 在里面进行封装

```js
import axios from 'axios';
export function request(config) {
    const instance = axios.create({
        baseURL: 'http://123.207.32.32:8000',
    })
    return instance(config) //返回的是一个promise
}

```

> 我们在main.js里面使用这个封装后的axios

```js
request({
  url:'/home/multidata',
  timeout:3000
}).then(res=>
console.log(res) //成功输出成功结果
).catch(err=>{
  console.log(err) //失败输出失败结果
})
```

# 6 axios拦截器的使用

**代码如下**

## axios请求拦截

```js
instance1.interceptors.request.use(config => {
    console.log(config)
    return config
    //如果config中的一些信息不符合要求可以在这里修改
    config.headers="application/json, text/plain, */*"
    //每次发送网络请求，都在界面中显示一个图标或者其他操作
    //某些网络请求，比如登陆，必须携带一些特殊的信息
}, err => {
    console.log(err)
})
```

## axios响应拦截

```js
instance1.interceptors.response.use(config=>{
    return config.data
},
    err=>{
    console.log(err)
    })
```

> 和请求拦截一样，只是一个请求时触发，一个响应时触发

