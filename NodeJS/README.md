# Node.js 



## 1.Node.js 介绍

### 为什么要学node.js

- 企业需求
  - 具有服务端开发经验更好
  - front-end
  - back-end
  - 全栈开发工程师
    - 全干
- 打开服务端的黑盒子，懂了解服务端 更好的协同开发
- 基本的网站开发能力
  - 服务端
  - 前端
  - 运维部署
- 多人社区(案例)

### Node.js 是什么

**Node.js® is a JavaScript runtime built on [Chrome's V8 JavaScript engine](https://v8.dev/).**

+ Node.js  不是一门语言
+ Node.js  不是库、不是模板
+ Node.js 是一个 JavaScript 运行时环境
+ 简单来说就是 Node.js 可以解析和执行 JS 代码
+ 以前只有浏览器可以解析执行 JS 代码
+ 也就是说，现在的 JS 可以完全脱离浏览器来运行，一切都归功与 Node.js
+ 构建与chromeV8引擎之上
  + 代码只是具有特定格式的字符串
  + 引擎可以帮我们解析和执行
  + Google Chrome V8 引擎是目前公认的解析执行 JS 代码最快最高效的
  + node.js 作者把 Google Chrome V8 引擎 移植出来 开发了一个独立的 JS 运行时环境

**浏览器中的 JS 和 Node.js 中的 JS 的区别**

+ 浏览器中的 JS
  + EcmaScript
    + 基本语法
    + if var function...
  + BOM
  + DOM
+ Node.js 中的 JS
  + **没有 BOM、DOM**
  + EcmaScript
  + 在 Node 这个 JS 执行环境中 为 JS 提供了一些服务器级别的操作 API
    + 例如 文件的读写
    + 网络服务的构建
    + 网络通信
    + http服务器
    + ...



**Node.js uses an event-driven,non-blocking I/O model that makes it lightweight and efficient**

+ event-driven 事件驱动
+ non-blocking I/O 非阻塞IO模型（异步）
+ lightweight and efficient 轻量和高效



**Node.js' package ecosystem,npm is the largest ecosystem of open source libraries in the world**

+ npm 是世界上最大的开源库生态系统
+ 绝大多数 JS 相关的 包都存放在 npm 上，这样做的目的是为了 让开发人员更方便的去下载使用
+ `npm install jquery`

### Node.js 能做什么

+ web 服务器后台
+ 命令行工具
  + npm(node)
  + git(c 语言)
  + hexo(node)
+ 对于前端开发工程师来说，接触Node 最多的是他的命令行工具
  + 自己写的很少，主要是使用别人的第三方的
  + webpack
  + gulp
  + npm

### 学习资源

+ 《深入浅出 Node.js》
  + 偏理论
+ 《Node.js 权威指南》
  + API讲解

+ 阮一峰的博客
+ [Node入门](https://www.nodebeginner.org/index-zh-cn.html)
+ [官方API文档](https://nodejs.org/api/)

### 学到什么

+ B/S 模型
  + Browser-Server
  + back-end
  + 任何服务端技术这种BS 编程都是一样的 和语言无关
  + Node 只是作为我们学习BS 编程模型的一个工具而已
+ 模块化编程
  + 在 Node 中可以像 `@import`一样来引用加载 JS 脚本文件
+ 常用的API
+ 异步编程
  + 回调函数
  + promise
  + async
  + generator
+ web开发框架
+ es6
  + 在课程中穿插讲解
+ ...



## 2.起步

### 安装

### hello world

#### 解析执行 JavaScript

1. 创建编写 JS 脚本文件

```js
var foo = 'helloNodeJS'
console.log(foo);
```

1. 打开终端，定位到脚本文件所属的目录
2. 输入`node 文件名` 执行对应的文件

![node](D:\workplace\总结\NodeJS\图片\git-node.png)



#### 读写文件

```js
// 浏览器中的 JavaScript 是没有文件操作能力的
// 但是 Node 中的 JavaScript 具有文件操作的能力

// fs 是 file-system 的简写 就是文件系统的意思
// 在 Node 中 要进行文件操作 就必须 引入 fs这个核心模块
// 在 fs 核心模块中 就提供了所有文件操作相关的API
// 例如：fs.readFile 就是用来 读取文件的

//1.使用  require 方法加载 fs 核心模块
var fs = require('fs')

// 2. 读取文件
//    第一个参数就是要读取的文件路径
//    第二个参数是一个回调函数
//       成功
//         data 是数据
//         error null
//       失败
//         data undefined
//         error 错误对象

fs.readFile('./data/hello',function(error,data){
    // <Buffer 68 65 6c 6c 6f 20 4e 6f 64 65 4a 73>
    // 文件中存储的是其实都是二进制的数据 0 1
    // 我们可以通过 toString 方法 来转换
    // console.log(data.toString());
    // console.log(error);
    //通过error来判断是否有错误发生
    if(error){
        console.log('读取文件失败');
    }else{
        console.log(data.toString());
    }
})
```

```js
var fs = require('fs')
//第一个参数 文件路径
//第二个参数 文件内容
//第三个参数 回调函数
//   error
//      成功 文件写入成功 error为null
//      错误 文件写入失败 error是错误对象
fs.writeFile('./data/hi','hi!!!',function (error) {
    if(error){
        console.log('写入文件失败');
    }else {
        console.log('写入文件成功');
    }
    // console.log(error);
})
```



#### 简单的http请求

```js
// 你可以用 Node 非常轻松的构建一个 Web 服务器
// 在 Node 中 专门提供了一个 核心模块： http
// http 这个模块的职责就是帮你创建编写服务器的

// 1. 加载 http 核心模块
var http = require('http')
//2. 使用 http.createServer() 方法创建一个 Web 服务器
var server = http.createServer()

//3. 服务器要干嘛？
//  提供服务 对数据的服务
//  发送请求--接收请求--处理请求--发送响应

// 注册 request 请求事件
//  当客户端请求过来，就会自动触发服务器的 request 请求事件，然后执行第二个参数，回调处理
server.on('request',function () {
    console.log('收到客户端请求');
})

//4. 绑定端口号 启动服务器
server.listen(3000,function () {
    console.log('服务器启动成功了，可以通过 http://127.0.0.1:3000/ 来进行访问');
})
```



![http](D:\workplace\总结\NodeJS\图片\http.png)



#### 升级版http请求

```js
var http = require('http')

var server = http.createServer()
/*
*   request 请求事件处理函数，需要接受两个参数：
*       Request 请求对象
*           请求对象可以用来获取客户端的一些请求信息，例如请求路径
*       Response 响应对象
*           响应对象可以用来给客户端发送响应消息
* */
server.on('request',function (request,response) {
    console.log('收到客户端请求,请求路径是：' + request.url)
    if(request.url === '/'){
        response.write('hello')
        response.end()
    }else if(request.url === '/login'){
        response.write('login')
        response.end()
    }
    // response 对象有一个方法： write可以用来给客户端发送响应数据
    // write 可以使用多次，但是最后一定要使用 end 来结束响应，否则客户端会一直等待
    // response.write('hello ')
    // response.write('NodeJs')
    // 告诉客户端 我的话说完了 你可以呈递给用户了
    // response.end()
})

server.listen(3000,function () {
    console.log('服务器启动成功了，可以通过 http://127.0.0.1:3000/ 来进行访问');
})
```

![http-up](D:\workplace\总结\NodeJS\图片\http-up.png)

#### response中的输出问题

```js
var http = require('http')

var server = http.createServer()

server.on('request',function (req,res) {
    var url = req.url;
    if(url === '/'){
        res.end('hello')
    }else if(url === '/products'){
        var products = [
            {
                name:'iphone',
                price:8000
            },
            {
                name:'huawei',
                price:5000
            },
            {
                name:'诺基亚',
                price:500
            }
        ]
        // 只能是 二进制或者 字符串
        res.end(JSON.stringify(products))
    }
})

server.listen(3000,function () {
    console.log('服务器启动成功了，可以通过 http://127.0.0.1:3000 来进行访问');
})
```



## 3.Node 中的 JavaScript

在 Node.js 中 模块有三种：

+ 核心模块
+ 自定义模块
+ 第三方模块



### 核心模块

Node 为 JavaScript 提供了很多服务器级别的 API，这些 API 绝大多数都是被包装到了一个具名的核心模块中

例如 文件操作的 `fs`核心模块、http服务构建的`http`模块、`path`路径操作模块、`os`操作系统信息模块

```js
var fs = require('fs')
var http = require('http')
```



### 自定义模块

+ Node.js 中没有全局作用域，只有**模块作用域**，模块内部不能访问外部变量和方法，反之亦然

+ **require **是一个方法，有两个作用
  + 加载文件模块并执行里面的代码
  + 拿到被加载文件模块导出的接口对象
  + 使用 require 来加载自定义模块时，要是使用相对路径，后缀名可以省略  `var b = require('./b');` 
+ 每个文件模块都提供了一个j接口对象 **exports**
  + require方法返回的是exports接口对象
  + exports默认是一个空对象
  + 我们可以将需要被外部访问的变量和函数挂载到exports接口对象上

a.js

```js
var b = require('./b');
console.log(b.foo);
console.log(b.add(1,2));
```

b.js

```js
var foo = 'bbbb';
function add(x,y){
    return x+y;
}
exports.foo = foo;
exports.add = add;
```



### 第三方模块



## 4.http



### 端口号

+ ip地址定位计算机

+ 端口号定位具体的应用程序



### [Content-Type](http://tool.oschina.net/)

+ 服务器最好要将每次响应的数据是什么内容类型告诉客户端
+ 不同的资源对应的 Content-Type 是不一样的
+ 文本类型最好要加上编码 目的是为了防止中文解析乱码，图片等非文本类型最好不要加编码

```js
var http = require('http');
http.createServer(function (request,response) {
    // 在服务器默认发送的数据，其实是 utf8 编码的内容
    // 但是浏览器不知道你是 utf8 编码的内容
    // 浏览器在不知道服务器响应内容的编码的情况下会按照当前操作系统的默认编码去解析
    // 中文操作系统默认是 gbk
    // 解决方法 就是 正确的告诉浏览器我给你发送的数据是什么编码
    var url = request.url;
    if( url === '/plain'){
        response.setHeader('Content-Type', 'text/plain; charset=utf-8');
        response.write('hello 世界');
        response.end();
    }else if( url === '/html'){
        response.setHeader('Content-Type', 'text/html; charset=utf-8');
        response.end('<h1>hello 世界</h1>');
    }
}).listen(3000,function () {
    console.log('server is running http://127.0.0.1:3000');
});
```



### 通过网络发送文件

+  发送的并不是文件了，本质上来讲发送的是文件的内容
+ 当浏览器接收到服务器响应内容之后，就会根据你的 Content-Type 进行对应的解析处理

```js
var http = require('http');
var fs = require('fs');

http.createServer(function (req,res) {
    var url = req.url;
    if(url === '/'){
        fs.readFile('./resource/index.html',function (err,data) {
            if(err){
                res.setHeader('Content-Type','text/plain;charset=utf-8');
                res.end('文件读取失败');
            }else{
                // data 默认是二进制的东西，可以通过 .toString 转为我们能识别的字符串
                // res.end() 支持两种数据类型：一种是二进制，一种是字符串
                res.setHeader('Content-Type','text/html;charset=utf-8');
                res.end(data);
            }
        });
    }else if(url === '/a'){
        fs.readFile('./resource/lw1.jpg',function (err,data) {
            if(err){
                res.setHeader('Content-Type','text/plain;charset=utf-8');
                res.end('文件读取失败');
            }else{
                // data 默认是二进制的东西，可以通过 .toString 转为我们能识别的字符串
                // res.end() 支持两种数据类型：一种是二进制，一种是字符串
                res.setHeader('Content-Type','image/jpeg');
                res.end(data);
            }
        });
    }
}).listen(3000,function () {
    console.log('server is running...');
});
```



### 请求对象Request

### 响应对象Response



### 在 Node 中使用模板引擎

```js
const http = require('http')
const fs = require('fs')
// 引入模板
const template = require('art-template')
const server = http.createServer()
server.on('request', (req, res) => {
    var wwwDir = 'D:/workplace/webstrom/nodejs/02-code/www'
    var url = req.url
    fs.readFile('./template2.html',(err, data) => {
        if (err) {
            return res.end('404 not found')
        }
        // 1. 如何获取 wwwDir 目录列表中的文件名和目录名
        //  	fs.readdir
        // 2. 如何将得到的文件名和目录名替换到 template.html 中
        //      模板引擎
        fs.readdir(wwwDir, (err, files) => {
            if (err) {
                return res.end('Can not find www dir')
            }
			// data 返回的数据是二进制
            data = template.render(data.toString(), {
                title: '模板引擎',
                files: files
            })

            res.end(data)
        })
    })
})
server.listen(4000, () => {
    console.log('server is running, you can visit this web by http://127.0.0.1:4000')
})
```

模板  template2.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{{ title }}</title>
</head>
<body>
<table>
    <h1>D:\workplace\webstrom\nodejs\02-code\www\的索引</h1>
    <hr>
    <thead>
    <tr class="header" id="theader">
        <th onclick="javascript:sortTable(0);">名称</th>
        <th>
        </th>
        <th class="detailsColumn" onclick="javascript:sortTable(2);">
            修改日期
        </th>
    </tr>
    </thead>
    <tbody id="tbody">
        {{ each files }}
            <tr>
                <td><a href="">{{ $value }}</a></td>
                <td>2019-01-01</td>
            </tr>
        {{ /each }}
    </tbody>
</table>
</body>
</html>
```



### 服务端渲染(SSR) VS 客户端渲染(CSR) VS 同构

|      | 服务端渲染                              | 客户端渲染                                     |
| ---- | :-------------------------------------- | :--------------------------------------------- |
| 利   | 首屏渲染快，客户端只负责解析html        | 前后端分离，前端专注于UI,后端专注于逻辑        |
|      | 利于 SEO 搜索引擎优化，容易被爬虫抓取到 | 局部刷新，无需每次请求完整页面，体验更好       |
|      | 可以生成缓存片段 静态文件               | 交互性能好，同时节省服务器资源                 |
| 弊   | 用户体验较差                            | SEO 问题，很难被爬虫抓取到                     |
|      | 不易于维护                              | 首屏渲染慢，渲染前，需要下载一堆 js 和 css文件 |



### 统一处理静态资源

#### 静态资源是什么

当浏览器收到 HTML 请求时，就会从上到下进行解析

在解析过程中，如果发现

`link` 、 `script` 、 `img`  、`iframe`  、 `video` 、`audio` 

等具有 `src` 或 `href(link)`  属性标签（外链资源）时，浏览器会自动对这些静态资源进行请求

#### 怎样统一处理静态资源

为了方便统一管理静态资源，我们约定把所有的静态资源放在 public 目录中

哪些资源能被访问到，哪些资源不能被访问到，我们现在可以通过代码来进行非常灵活的控制

```js
const http = require('http')
const fs = require('fs')
const template = require('art-template')
http.createServer((req, res) => {
    var url = req.url
    if (url === '/') {
        fs.readFile('./views/index.html', (err, data) => {
            if (err) {
                return res.end('404 not found')
            }
            res.end(data)
        })
    } else if (url.indexOf('/public/') === 0) {
        /*
        *  /public/css/main.css
        *  /public/js/main.js
        *  /public/lib/jquery.js
        *  统一处理：
        *       如果请求路径是以 /public/开头的，则我认为你要获取 public 中的某个资源
        *       所以我们就直接可以把请求路径当做文件路径来直接进行读取
        * */
        fs.readFile('.' + url, (err, data) => {
            if (err) {
                return res.end('404 not found')
            }
            res.end(data)
        })
    }
}).listen(3000, () => {
    console.log('server is running!You can visit our web by http://127.0.0.1:3000')
})
```

#### HTML中静态资源

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>留言板</title>
    <!--
        注意：在服务端中，文件中的路径就不要去写相对路径了
        因为这个时候所有的资源都是通过 url 标识来获取
        我的服务器开放了 /public 目录
        所以这里的请求路径都写成： /public/xxx
        / 这里就是 url 根路径的意思
        浏览器在真正发请求的时候会最终把 http://127.0.0.1:3000 评上

        不要再想文件路径，把所有的路径都想象成 url 地址
    -->
    <link rel="stylesheet" href="/public/lib/bootstrap3/dist/css/bootstrap.css">
    <link rel="stylesheet" href="/public/css/main.css">
</head>
<body>
<div class="header container">
    <div class="page-header">
        <h1>Example page header <small>Subtext for header</small></h1>
        <a class="btn btn-success" href="post.html">发表留言</a>
    </div>
</div>
<div class="comments container">
    <div class="row">
        <div class="col-lg-9">
            <ul class="list-group">
                <li class="list-group-item">Cras justo odio</li>
                <li class="list-group-item">Dapibus ac facilisis in</li>
                <li class="list-group-item">Morbi leo risus</li>
                <li class="list-group-item">Porta ac consectetur ac</li>
                <li class="list-group-item">Vestibulum at eros</li>
            </ul>
        </div>
    </div>
</div>
<script src="/public/js/main.js"></script>
</body>
</html>
```



#### 番外  用户访问的流程

用户点击网址，向服务端请求根目录下的资源（此处为 index.html)，若碰到访问静态资源时（链接地址为资源的url 地址），浏览器会向服务器请求静态资源，此时，我们将 public 目录公开，静态资源得以访问（在django+Python中就是 static 目录），进行样式/行为的渲染



## 5.留言板

在 `app.js` 中，是服务端代码

```js
// app application 应用程序
// 当前模块所有的依赖项都声明在文件模块的最上面
// 为了让目录解构保持一致清晰，所以我们约定，把所有的 HTML 文件都放到 views 目录中
// 为了方便的统一处理这些静态资源，我们约定把所有的静态资源都存放在 public 目录中
// 哪些资源能被用户访问到，哪些资源不能被用户访问，我现在可以通过代码来进行非常灵活的控制

const http = require('http')
const fs = require('fs')
const url = require('url')
const template = require('art-template')

var comments = [
    {
        name: '张三',
        message: '今天天气好不错',
        dateTime: '2019-1-1'
    },
    {
        name: '张三2',
        message: '今天天气好不错',
        dateTime: '2019-1-1'
    },
    {
        name: '张三3',
        message: '今天天气好不错',
        dateTime: '2019-1-1'
    },
]

// http://127.0.0.1:3000/comments?name=11&content=111111'
// 对于这种表单提交的请求路径，由于其中具有用户动态填写的内容
// 所以你不可能通过去判断完整的 url 路径来处理这个请求
// 结论： 如果请求的路径是 /comments 的时候 我们就认为请求表单的数据过来了

http.createServer((req, res) => {
    var parseObj = url.parse(req.url, true)
    var pathname = parseObj.pathname
    if (pathname === '/') {
        fs.readFile('./views/index.html', (err, data) => {
            if (err) {
                return res.end('404 not found')
            }
            data = template.render(data.toString(), {
                comments: comments
            })
            res.end(data)
        })
    } else if (pathname === '/post') {
        fs.readFile('./views/post.html', (err, data) => {
            if (err) {
                return res.end('404 not found')
            }
            res.end(data)
        })
    } else if (pathname.indexOf('/public/') === 0) {
        /*
        *  /public/css/main.css
        *  /public/js/main.js
        *  /public/lib/jquery.js
        *  统一处理：
        *       如果请求路径是以 /public/开头的，则我认为你要获取 public 中的某个资源
        *       所以我们就直接可以把请求路径当做文件路径来直接进行读取
        * */
        fs.readFile('.' + pathname, (err, data) => {
            if (err) {
                return res.end('404 not found')
            }
            res.end(data)
        })
    } else if (pathname === '/comments') {
        // res.end(JSON.stringify(parseObj.query))
        /*
        *   1.获取表单提交的数据 parseObj
        *   2.将当前时间日期到数据对象中，然后存储到数组中
        *   3.让用户重新定向跳转到首页 /
        *       当用户重新请求 / 的时候，数组中的数据已经发生变化了
        * */
        var comment = parseObj.query
        comment.dateTime = '2019-01-01'
        comments.unshift(comment)
        // 服务端这个时候已经把数据存储好了，接下来就是让用户重新请求 / 首页

        // 如何通过服务器让客户端重定向？
        // 1.状态码设置为 302 临时重定向
        //      statusCode
        // 2.在响应头中通过 Location 告诉客户端往哪里重定位
        //      setHeader
        // 客户端发现收到服务器的响应的状态码为 302 就会自动去响应头中找到 Location 然后对该地址发起新的请求
        // 所以就看到客户端自动调整
        res.statusCode = 302
        res.setHeader('Location', '/')
        res.end()
    } else {
        fs.readFile('./views/404.html', (err, data) => {
            if (err) {
                return res.end('404 not found')
            }
            res.end(data)
        })
    }
}).listen(3000, () => {
    console.log('server is running!You can visit our web by http://127.0.0.1:3000')
})
```

注：url.parse的细节

![url](D:\workplace\总结\NodeJS\图片\url-parse.png)

url.parse('路径') --- 得到一个对象

url.pathname  url路径

url.query 是一个对象，表单输入的内容





将所有的HTML放在view文件夹中

index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>留言板</title>
    <!--
        浏览器收到 html 响应内容之后，就要从上到下一次解析
        当解析的过程中，如果发现
        link
        script
        img
        iframe
        video
        audio
        等具有 src 或者 href(link) 属性标签（外链资源）时，浏览器会自动对这些静态资源发起新的请求
    -->
    <!--
        注意：在服务端中，文件中的路径就不要去写相对路径了
        因为这个时候所有的资源都是通过 url 标识来获取
        我的服务器开放了 /public 目录
        所以这里的请求路径都写成： /public/xxx
        / 这里就是 url 根路径的意思
        浏览器在真正发请求的时候会最终把 http://127.0.0.1:3000 评上

        不要再想文件路径，把所有的路径都想象成 url 地址
    -->
    <link rel="stylesheet" href="/public/lib/bootstrap3/dist/css/bootstrap.css">
</head>
<body>
<div class="header container">
    <div class="page-header">
        <h1>Example page header <small>Subtext for header</small></h1>
        <a class="btn btn-success" href="/post">发表留言</a>
    </div>
</div>
<div class="comments container">
    <div class="row">
        <div class="col-lg-9">
            <ul class="list-group">
                {{ each comments }}
                <li class="list-group-item">
                    {{ $value.name }}说：{{ $value.message }}
                    <span class="pull-right">{{ $value.dateTime }}</span>
                </li>
                {{ /each }}
            </ul>
        </div>
    </div>
</div>
</body>
</html>
```

post.html

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>post</title>
    <link rel="stylesheet" href="/public/lib/bootstrap3/dist/css/bootstrap.css">
</head>
<body>
<div class="header container">
    <div class="page-header">
        <h1><a href="/"> 首页 </a><small>Subtext for header</small></h1>
    </div>
</div>
<div class="container">
    <div class="row">
        <div class="col-lg-9">
            <!--
                以前表单是如何提交的？
                表单中的需要提交的表单控件必须具有 name 属性

                表单提交分为：
                    1. 默认的提交行为
                    2. 表单异步提交

                action 就是表单提交的地址，说白了就是请求的 url 地址
                method 请求方法
                    get
                    post
            -->
            <form action="/comments" method="get">
                <div class="form-group">
                    <label for="name">Your Name</label>
                    <input type="text" class="form-control" id="name" name="name" required="required" minlength="2" maxlength="10"  placeholder="Your Name">
                </div>
                <div class="form-group">
                    <label for="content">Content</label>
                    <textarea class="form-control" rows="3" id="content" name="message" required="required" minlength="5" maxlength="20" ></textarea>
                </div>
                <button type="submit" class="btn btn-primary">Submit</button>
            </form>
        </div>
    </div>
</div>
</body>
</html>
```



将静态资源放置public文件夹中，公开给客户端访问



## 6.Node中的核心模块

使用Node 编写应用程序主要就是在使用

+ ES语言
  + 和浏览器不一样，在Node中没有 DOM 和 BOM
+ 核心模块
  + 文件操作 fs
  + http服务的http
  + url 路径操作模块
  + path 路径处理模块
  + os 操作系统信息
+ 第三方模块
  + art-template
  + 通过npm来下载才能用



### 6.1 什么是模块化

+ 文件作用域
+ 通讯规则
  + 加载 require
  + 导出

### 6.2 CommonJS 模块规范

在 node 中的 JavaScript 还有一个很重要的概念：模块系统

+ 模块作用域
+ 使用 require 方法来加载模块
+ 使用 exports 接口对象用来导出模块中的成员

#### 6.2.1 加载 `require`

语法：

```js
var 自定义变量名 = require('模块')
```

两个作用：

+ 执行被加载模块中的代码
+ 得到被加载模块中 `exports` 导出接口对象

#### 6.2.2 导出 `exports`

+ node中的模块作用域，默认文件中所有成员只在当前模块有效
+ 对于希望被其他模块访问的成员，我们就需要把这些公开的成员都挂载到 `exports`接口对象中就可以

导出多个成员(必须在对象中)

```js
exports.add = add
exports.c = 'hello'
...
```

导出单个成员（拿到的就是：函数，字符串）

```js
module.exports = 'hello'
```

以下情况会覆盖：

```js
module.exports = 'hello'
// 以这个为准，后者会覆盖前者
module.exports = add
```

也可以这样来导出多个成员

```js
module.exports = {
    add: add,
    str: 'hello'
}
```

#### 6.2.3 原理解析

```js
// 在 Node 中，每个模块都有一个自己的 module 对象
// 该module对象中，有一个成员为 exports 也是一个对象
// 也就是说 如果你要对外导出成员，只需要把导出的成员挂载到 module.exports上

// var module = {
//     exports: {
//        foo: 'bar'
//     }
// }

// module.exports.foo = 'bar'
// module.exports.add = (x, y) => {
//     return x + y
// }

// Node 为了简化用户操作，提供了一个 exports = module.exports
// 也即模块中还有一句代码
// var exports = module.exports

// 两者一致，说明可以用任一方来导出成员
// console.log(exports === module.exports) // true

module.exports.foo = 'bar'
exports.add = (x, y) => {
    return x + y
}

// 谁来 require 谁就得到 module.exports
// 默认在代码的最后一句
// 最后 return 的是 module.exports 
// return module.exports
```



`exports` 是 `module.exports` 的一个引用

```js
// 如果使用以下方法导出单个成员是不行的
exports = 'hello'
// 原因是
// exports只是 module.exports 的一个引用，最终返回的 module.exports
// exports = 'hello' 此时 exports 指向的是一个新的对象，已经不再与 module.exports 有任何关系

// 导出单个成员的的正确方法
module.exports = add
```



#### 6.2.4 exports 和 module.exports之间的差别

- 每个模块都有一个module对象
- module对象中有一个 exports 对象
- 我们可以把需要导出的成员都挂载到 module.exports 接口对象中
- 也就是 `module.exports.xxx = xxx` 的方式
- 但是 每次  `module.exports.xxx = xxx`  很麻烦 点很多
- 所以 Node 为了方便 在每个模块中 都提供了一个成员叫 `exports`
- `exports === module.exports`  结果为 true
- 所以对于  `module.exports.xxx = xxx`的方式，完全可以写成： `exports.xxx = xxx`
- 当一个模块需要导出单个成员时，这个时候必须使用  `module.exports = xxx`的方式
- 因为 每个模块最终导出的是  `module.exports`
- 而 `exports`只是 `module.exports`的一个引用
- 所以即使你为  `exports = xxx` 重新赋值，也不会影响  `module.exports`
- 但是 若使得 `exports` 重新指向 `module.exports` 则会重新建立引用关系，回到初始状态



#### 6.2.5 require 方法加载规则

##### 1. 优先从内存中加载

main.js

```js
require('./a')
require('./b')
// 优先从缓冲中加载
// 由于 在 a 中已经重复加载过了 b
// 所以这里不会重复加载
// 可以拿到导出的对象，但是不会执行之间的代码
// 这样做 是为了避免重复加载 提高模块加载效率

// 运行main.js 得到的是 
// a.js 被加载了
// b.js 被加载了
```

a.js

```js
console.log('a.js 被加载了')
require('./b')
```

b.js

```js
console.log('b.js 被加载了')
```



##### 2. 判断模块标识

+ 核心模块

  ```js
  // 核心模块的本质也是文件
  // 核心模块文件已经被编译到了二进制文件中，我们只需要按照名字来加载就可以了
  require('fs')
  require('http')
  ```

+ 第三方模块

  ```js
  //第三方模块
  // 凡是第三方模块都必须通过 npm 下载
  // 使用的时候就可以通过 require('包名') 的方式来进行加载才可以使用
  // 不可能有任何一个第三方包和核心模块的名字是一样的
  // 既不是核心模块 也不是 路径形式
  
  //  	先找到当前文件所在目录中的 node_ modules 目录
  //  	node_modules/art-template
  //  	node_modules/art-template/package.json 文件
  //  	node_modules/art-template/package.json 文件中的 main 属性
  //  	main属性中就记录了 art-template 的入口模块
  //  	然后加载使用这个第三方包
  //  	实际上最终加载的还是文件
  
  // 		如果 package.json 文件不存在或者 main 指定的入口模块也没有
  //      则 node 会自动找该目录下的 index.js
  //      也就是说 index.js 会被作为一个默认备选项
  
  //      如果以上所有任何一个条件都不成立 则会进入上一级目录中的 node_module 目录查找
  //      如果再上一级目录页没有 则会继续往上上级查找
  //      ...
  //      知道当前磁盘根目录还没有找到，最后报错
  
  // 注意： 我们项目中有且只有一个 node_modules，放在项目的根目录中，这样的话项目中所有的子目录都可以访问的到
  
  var template = require('art-template')
  ```

+ 自己写的

  ```js
  // 路径形式的模块标识：
  // ./ 表示当前目录下 不可省略
  // ../ 表示 上一级目录 不可省略
  require('./foo.js')
  ```

  

### 6.3 npm

#### 6.3.1npm 网站

> npmjs.com

#### 6.3.2 npm 命令行工具

npm第二层含义就是一个命令行工具，只要你安装了node 就已经安装了 npm

npm 也有版本的概念

可以通过在命令行输中输入

```js
npm --version
```

升级 npm

```js
npm install --global npm
```

#### 6.3.3 常用命令

+ npm init
  + npm init -y 可以跳过向导 快速生成
+ npm install
  + 一次性将 dependencies 选项中的依赖项全部安装
  + npm i
+ npm install 包名
  + 只下载
  + npm i 包名
+ npm install --save 包名
  + 下载并保存依赖项（package.json文件中的 dependencies 选项）
  + npm i -S 包名
+ npm uninstall 包名
  + 只删除，如果有依赖项会依然保存
  + npm un 包名
+ npm uninstall --save 包名
  + 删除的同时 会将依赖信息也删除
  + npm un -S 包名
+ npm help
  + 寻求帮助
+ npm 命令 --help
  + 例如 npm uninstall --help
  + 查看制定命令的使用帮助



#### 6.3.4 解决npm被墙的问题

npm 存储包文件的服务器被墙的问题

https://npm.taobao.org/ 

安装淘宝的cnpm

```js
npm install --global cnpm
```



### 6.4 package.json

我们建议每个项目都要有一个 `package.json`文件（包描述文件，就像产品的说明书一样），给人踏实感

这个文件可以通过  `npm init`  的方式初始化出来

```js
D:\workplace\webstrom\nodejs\03-code\05-package>npm init
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg>` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
package name: (05-package)
version: (1.0.0) 0.0.1
description: 这是一个测试包
entry point: (index.js)
test command:
git repository:
keywords:
author: cj
license: (ISC)
About to write to D:\workplace\webstrom\nodejs\03-code\05-package\package.json:

{
  "name": "05-package",
  "version": "0.0.1",
  "description": "这是一个测试包",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "cj",
  "license": "ISC"
}


Is this OK? (yes)
```

对于目前来说，最有用的是 `dependencies` 选项，可以用来帮我们保存第三方包的依赖信息

当使用 `npm install --save 包名` 或者 `npm install 包名 --save` 指令时，会自动在`package.json` 中更新`dependencies` 属性值

```js
npm install --save 包名
npm install 包名 --save
```

```js
{
  "name": "05-package",
  "version": "0.0.1",
  "description": "这是一个测试包",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "cj",
  "license": "ISC",
  "dependencies": {
    "art-template": "^4.13.2"
  }
}
```

当不小心将 `node_modules` 包删除时，我们可以通过`npm install ` 将所有依赖的包重新装回来

```js
npm install
```



## 7.Express

原生的http在某些方面不足以应对我们的开发需求，所以我们就需要使用框架来加快我们的开发框架，框架的目的就是提高效率

初步感知

```js
//  0.安装
//  1.引包
const express = require('express')

// 2.创建你服务器应用程序
//      也就是原来的 http.createServer
const app = express()
const port = 3000

// 公开指定目录
// 只要这样做了 你就可以直接通过 /public/xx 的方式访问 public 目录下的所有资源了
app.use('/public/',express.static('./public/'))

app.get('/', (req, res) => {
    res.send('hello express!')
})

app.get('/about', (req, res) => {
    res.send('你好，我是express  !')
})

app.listen(port, () => {
    console.log('app is running at port 3000')
})
```



### 7.1 起步

#### 7.1.1 安装：

```shell
npm install --save express
```

#### 7.1.2 hello world:

```js
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => {
    res.send('hello world hello')
})

app.listen(port, () => {
    console.log('express app is running')
})
```

#### 7.1.3 基本路由

路由器

+ 请求方法
+ 请求路径
+ 请求处理函数

get：

```js
// 当你以 GET 方法请求 / 的时候，执行对应的处理函数
app.get('/', (req, res) => {
    res.send('hello world hello')
})
```

post：

```js
// 当你以 POST 方法请求 / 的时候，执行对应的处理函数
app.post('/', (req, res) => {
    res.send('Got a POST request')
})
```

#### 7.1.4 静态服务

```js
// /public/xxxx
app.use('/public/',express.static('./public/'))

// /static/xxx
app.use('/static/',express.static('./public/'))

// /public资源
app.use(express.static('./public/'))
```



### 7.2 在 Express 中配置使用 `art-template` 模板引擎

安装

```shell
npm install --save art-template
npm install --save express-art-template
```

配置

```shell
app.engine('html', require('express-art-template'))
```

使用

```js
app.get('/', (req, res) => {
    // Express 默认会去项目中的views 目录查找该模板文件
    res.render('index.html',{
        title: 'hello world'
    })
})
```



### 7.3 在 Express 中获取表单 GET 请求数据

在 Express 中内置了一个 API，可以直接通过 `req.query` 来获取

```js
req.query
```



### 7.4 在 Express 中获取表单 POST 请求数据

在 Express 中 没有内置获取表单 POST 请求体中的 API， 这里我们需要安装一个第三方包： `body-parser`

安装

```shell 
npm install body-parser
```

配置

```js
var express = require('express')
// 0. 引包
var bodyParser = require('body-parser')

var app = express()
// 1.配置 body-parser
// 只要加入这个配置，则在 req 请求对象上会多出来一个属性 body
// 也就是说你就可以通过 req.body 来获取表单 POST 请求体数据了
// parse application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({ extended: false }))
// parse application/json
app.use(bodyParser.json())

app.use(function (req, res) {
  res.setHeader('Content-Type', 'text/plain')
  res.write('you posted:\n')
  res.end(JSON.stringify(req.body, null, 2))
})
```



### 7.5 使用 Express 框架来实现留言板

```js
const express = require('express')
const app = express()
const port = 3000
const bodyParser = require('body-parser')

var comments = [
    {
        name: '张三',
        message: '今天天气好不错',
        dateTime: '2019-1-1'
    },
    {
        name: '张三2',
        message: '今天天气好不错',
        dateTime: '2019-1-1'
    },
    {
        name: '张三3',
        message: '今天天气好不错',
        dateTime: '2019-1-1'
    }
]

// 公开 public 目录
app.use('/public/',express.static('./public/'))

// 配置使用 art-template 模板引擎
app.engine('html', require('express-art-template'))

// 配置 body-parser
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())

// Express 为 Response 相应对象提供了一个方法： render
// render 方法默认是不可以使用的，但是如果配置了模板引擎就可以使用了
// res,render('html模板名', {模板数据}）
// 第一个参数不能写路径，默认回去项目中的views 目录查找该模板文件
// 也即，Express 有一个约定：开发人员把所有的视图文件都放在 Views 目录中
app.get('/', (req, res) => {
    res.render('index.html', {
        comments: comments
    })
})

app.get('/post', (req, res) => {
    res.render('post.html')
})

// 当以 POST 请求 /post 的时候，执行指定的处理函数
// 这样我们可以利用不同的请求方法让一个请求路径使用多次
app.post('/post', (req, res) => {
    var comment = req.body
    comment.dateTime = '2019-01-01'
    comments.unshift(comment)
    res.redirect('/')
    // res.statusCode = 302
    // res.setHeader('Location','/')
} )

// app.get('/comments', (req, res) => {
//     var comment = req.query
//     comment.dateTime = '2019-01-01'
//     comments.unshift(comment)
//     res.redirect('/')
//     // res.statusCode = 302
//     // res.setHeader('Location','/')
// })

app.listen(port, () => {
    console.log('running...')
})
```



### 7.6 Express-CURD

#### 7.6.1 起步

+ 初始化
+ 模板处理

#### 7.6.2 路由设计

| 请求方法 | 请求路径         | get  参数 | post 参数                  | 备注             |
| :------- | :--------------- | :-------- | :------------------------- | :--------------- |
| GET      | /students        |           |                            | 渲染首页         |
| GET      | /students/new    |           |                            | 渲染添加学生页面 |
| POST     | /students        |           | name/age/gender/hobbies    | 处理添加学生请求 |
| GET      | /students/edit   | id        |                            | 渲染编辑页面     |
| POST     | /students/edit   |           | id/name/age/gender/hobbies | 处理编辑请求     |
| GET      | /students/delete | id        |                            | 处理删除请求     |

####  7.6.3 提取路由模块

```js
/*
* router.js 路由模块
* 职责：
*   处理路由
*   根据不同的请求方法 和请求路径 来设置不同的处理函数
*
* 模块职责要单一，不要乱写
* 我们划分模块的目的就是为了 增强项目代码的可维护性
* 提升开发效率
*
* */

const fs = require('fs')

// 1. 创建一个路由容器
const express = require('express')
const router = express.Router()

// 2. 把所有的路由都挂载到路由容器中
router.get('/students', (req, res) => {

})
router.get('/students/new', (req, res) => {
    
})

// 3. 导出 router
module.exports = router
```

app.js

```js
app.use(router)
```

#### 7.6.4 设计操作数据的 API 文件模块

```js
/*
*    students.js 数据操作模块
*    职责：
*       操作文件中的数据，只处理数据，不关心业务
* */

/*
* 获取所有学生列表
* return []
* */
exports.find = function () {

}

/*
* 添加保存学生
* */
exports.save = function () {

}

/*
* 更新学生
* */
exports.update = function () {

}

/*
* 删除学生
* */
exports.delete = function () {

}
```





## 8. MongoDB

表就是关系

或者说表与表之间存在的关系

+ 所有的关系数据库都是需要通过`sql` 语言来操作
+ 所有关系型数据库在操作之前都需要设计表结构
+ 而且数据表还支持约束
  + 唯一的
  + 主键
  + 默认值
  + 非空
+ 非关系型数据库非常的灵活
+ 有的非关系型数据库就是 key-value 对
+ 在 MongoDB 是长得最像关系型数据库的非关系型数据库
  + 数据库 =》数据库
  + 数据表 =》 集合（数组）
  + 表记录 =》 （文档对象）
+ MongoDB 不需要设计表结构
+ 也就是说你可以任意的往里面存数据，没有结构性一说



### 8.1 开启和关闭数据库

启动

```js
mongod
```

停止

```
开启服务的控制台，直接 Ctrl+c 即可停止
```



### 8.2 连接数据库

连接：

```shell
# 该命令默认链接本机的 MongoDB 服务
mongo
```

退出

```shell
exit
```



### 8.3 基本命令

+ `show dbs`
  + 查看显示所有的数据库
+ `db`
  + 查看当前操作的数据库
  + 默认会在 test 库下（注意此时并不存在这个库，因为没有数据，只是库名被创建了）
+ `use  数据库名称`
  + 切换到指定的数据库（如果没有会新建）
+ 插入数据



### 8.4 在 Node 中如何操作 MongoDB 包来操作

#### 8.4.1 使用官方的 `mongodb` 包来操作

> [npmjs上的官方包](https://www.npmjs.com/package/mongodb)

#### 8.4.2 第三方 mongoose 来操作 MongoDB 数据库

第三方包：`mongoose` 基于 MongoDB 官方的 `mongodb` 包再做了一次封装









## 番外

### 书籍

《编写可维护的javascript》

代码风格

[JavaScript Standard Style](https://standardjs.com/index.html)

[Airbnb JavaScript Style](https://github.com/airbnb/javascript/blob/master/README.md#functions)



```
// 当你采用了五分号的代码风格的时候，只需要注意以下三种情况
//  当一行代码是以：
//      （
//      [
//      `
//      开头时，则前面补上一个分号用以避免一些语法解析错误
// 结论：不论你有没有加分好的风格 碰到以上三种情况时 最好都在其前面加上 ;

function say () {
    console.log('say')
}
say()

// TypeError: say(...) is not a function
;(function () {
  console.log('hello')
})()

// TypeError: Cannot read property '香蕉' of undefined
;['苹果', '香蕉'].forEach(function (item) {
  console.log(item)
})

// ` 是ES6 中新增的一种字符串包裹方式，叫做 模板字符串
// 支持换行和非常方便的拼接

;`大家好 hello`.toString()
```



### each和forEach的区别

forEach()方法是ES5中提供的遍历数组的方法，只能够用来遍历数组

```js
arr = [1,2,3,4,5]
			arr.forEach((item, index) => {
				console.log(item)
			})
```

伪数组是指，具有索引和长度的对象

在对象的原型链（Object.prototype）中，没有forEach方法，因此不能用forEach方法来遍历伪数组

![foreach1](D:\workplace\总结\NodeJS\图片\foreach1.png)

若想用foreach来遍历伪数组，可以将伪数组转化为数组，使用以下方法

```js
[].slice.call($('div'))

Array.prototype.mySlice = function(){
    var start = 0
    var end = this.length
    if (arguments.length === 1) {
        start = arguments[0]
    } else if (arguments === 2) {
        start = arguments[0]
        end = arguments[1]
    }
    var temp = []
    for (var i = start; i < end; i++) {
        temp[i] = this[i]
    }
    return temp
} 
```

![foreach2](D:\workplace\总结\NodeJS\图片\foreach2.png)

![foreach3](D:\workplace\总结\NodeJS\图片\foreach3.png)



jQuery 提供的 each 方法 可以用来遍历数组和伪数组

```js
$('div').each((index,item) => {console.log(item)})
```

同时，可以在不兼容forEach 的低版本浏览器中使用 jQuery 的 each 方法



###  修改完代码自动重启

`nodemon` 是一个基于`Node.js` 开发的一个第三方命令行工具，可以帮助我们解决频繁修改代码重启服务器问题

```shell
// 在任意目录执行该指令都行
npm install --global nodemon
```

安装完毕之后 使用

```shell
node app.js

//使用 nodemon
nodemon app.js
```











