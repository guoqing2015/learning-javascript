# 关于cors

## HTTP 消息头的下面这个几个域

- `Origin`: 首部字段表明预检请求或实际请求的源站。
- `Access-Control-Allow-Credentials`: 首部字段用于预检请求的响应，表明服务器是否允许 credentials 标志设置为 true 的请求。
- `Access-Control-Allow-Origi`n: 指定了允许访问该资源的外域 URI。对于不需要携带身份凭证的请求，服务器可以指定该字段的值为通配符，表示允许来自所有域的请求。


## 啥是cors呢

`Cross-Origin Resource Sharing（cors）`，顾名思义，跨域资源共享，也就是一种实现跨域的手段

**跨域（cross-orgin）**是因为有`同源策略（same-origin policy）`的存在。浏览器为了保证加载的脚本等资源都是可控安全的，就加了一个强制性的规定，非同域名下的资源不得加载。同域名是指两个域名之间，`端口`，`协议`，以及`子域`都应相同。

`cors`的实现非常简单，前端甚至都不需要做修改，只需要在对方服务端的响应头里加个字段`Access-Control-Allow-Origin`就可以了。Access-Control-Allow-Origin的值可以是`*`，也就是代表任何域名都可以来拿我的资源，也可以指定域名，只有指定的域名可以拉取资源。


### 预请求（Preflighted）

预校验是指某些特殊情况下，需要在发送真正的ajax请求之前发送一个`options`方法的请求，来『探测』一下我们的请求服务端接不接受，当然这一步是浏览器自动的，无需手动。『特殊情况』就是指下面的两种情况：

 - 使用了get、head和post之外的方法，或post请求的Content-Type是application/x-www-form-urlencoded、multipart/form-data、text/plain以外的，如application/xml等时；
 - ajax请求带有自定义请求头时。

 在预请求的请求头里会额外再带上两个字段，分别是`Access-Control-Request-Method`（必带），`Access-Control-Request-Headers`(如果设置了自定义请求头)，Access-Control-Request-Method的值就是该ajax请求所用的方法

 ### 带上cookie的ajax跨域请求（credentials）

一般来说，跨域的ajax请求是不带cookie的，而cors的ajax请求就不一样了，人家可以带cookie去服务端，这样就可以让服务端来判断这个请求是否合法。而且让ajax带cookie很简单，一句话的事儿：`xhr.withCredentials = true;`。若服务端认为这个请求是合法的，返回的响应头里必须带上`Access-Control-Allow-Credentials: true`，若为其他值或不带这个字段，浏览器会把`withCredentials`发出的请求的响应通通拒绝掉。

需要注意是，当配置了`xhr.withCredentials = true`时，必须在后端增加 response 头信息`Access-Control-Allow-Origin`，且**必须指定域名**，而不能指定为*。当前示例所采用的node端配置代码：

```js
res.setHeader('Access-Control-Allow-Origin','http://172.19.0.215:3333');
```


## koa2处理方式

```js
import cors from '@koa/cors';
app.use(cors({
  origin: 'http://localhost:3000',
  credentials: true
}))
```

cors方法可以传入一个对象options

`
{
    origin：允许发来请求的域名，对应响应的`Access-Control-Allow-Origin`，
    allowMethods：允许的方法，默认'GET,HEAD,PUT,POST,DELETE'，对应`Access-Control-Allow-Methods`，
    exposeHeaders： 允许客户端从响应头里读取的字段，对应`Access-Control-Expose-Headers`，
    allowHeaders：这个字段只会在预请求的时候才会返回给客户端，标示了哪些请求头是可以带过来的，对应`Access-Control-Allow-Headers`，
    maxAge：也是在预请求的时候才会返回，标明了这个预请求的响应所返回信息的最长有效期，对应`Access-Control-Max-Age`
    credentials：标示该响应是合法的，对应`Access-Control-Allow-Credentials`
}
`