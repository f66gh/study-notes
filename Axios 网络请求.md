# Axios 网络请求

在实际项目开发中，几乎每个组件中都会用到axios发起数据请求，可以用全局配置的方式解决上述问题

```javascript
// 配置请求根路径
axios.defaults.baseURL = 'http://api.com'

//将axios作为全局的自定义属性，每个组件都可以在内部直接访问（vue3）
app.config.globalProperties.$http = axios

//将axios作为全局的自定义属性，每个组件可以在内部传值(vue2)
Vue.prototype.$http = axios
```

# 前端数据模拟MockJS

在前端模拟后端传过来的数据

# JWT跨域认证

## Session认证

* 用户向服务器发送用户名和密码
* 服务器验证通过后，在当前对话(session)里边保存相关数据，比如用户角色、登录时间等
* 服务器向用户返回一个session_id，写入用户的Cookie
* 用户随后的每一次请求，都会通过Cookie，将Session_id传回服务器
* 服务器收到了session_id，找到前期保存的数据，由此得知用户的身份

![image-20221223114028960](D:\WebStrom Coding\课件\image-20221223114028960.png)

session 认证的方式应用非常普遍，但也存在一些问题，扩展性不好，如果是服务器集群，或者是跨域的服务导向架构，就要求 session 数据共享，每台服务器都能够读取 session。用户可能在不同时间进行登录操作时是不同的服务器运行的，由于session_id是用来找服务器内部的数据不是数据本身，所以出现了用户需要重复登录的问题

## Token认证

token是在服务端产生的一串字符串，是客户端访问资源接口时所需要的资源凭证，流程如下

* 客户端使用用户名跟密码请求登录，服务端收到请求，去验证用户名与密码
* 验证成功后，服务端会签发一个 token 并把这个 token 发送给客户端
* 客户端收到 token 以后，会把它存储起来，比如放在 cookie 里或者 localStorage 里
* 客户端每次向服务端请求资源的时候需要带着服务端签发的 token
* 服务端收到请求，然后去验证客户端请求里面带着的 token ，如果验证成功，就向客户端返回请求的数据

![image-20221223114738708](D:\WebStrom Coding\课件\image-20221223114738708.png)

这种情况下数据没有给服务器进行保留

## JWT

* JSON Web Token（简称 JWT）是一个token的具体实现方式，是目前最流行的跨域认证解决方案。

* JWT 的原理是，服务器认证以后，生成一个 JSON 对象，发回给用户，具体如下：

