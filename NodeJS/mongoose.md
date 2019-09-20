# mongoose

+ 官网：[https://mongoosejs.com](https://mongoosejs.com/)
+ 官方指南：https://mongoosejs.com/docs/guide.html



## 1. MongoDB 数据库的基本概念

+ 数据库
+ 集合（collections）
+ 文档

```js
// mongodb -- 对象
{
    // 数据库 -- 对象
    qq:{
       // 集合 -- 数组
       users:[
           // 文档 -- 对象
           {name:'zs',age:15},
           {name:'zs2',age:15},
           {name:'zs3',age:15},
           {name:'zs4',age:15},
           ...
       ],
       products:[
           
       ]
    },
    taobao:{
        
    },
    baidu:{
        
    }
}
```



## 2. 起步

安装

```shell
npm i mongoose
```

hello world

```js
const mongoose = require('mongoose')
// 连接 MongoDB 数据库
mongoose.connect('mongodb://localhost:27017/test', {useNewUrlParser: true})

// 创建一个模型
// 就是在设计 数据库
const Cat = mongoose.model('Cat', { name: String })
// 实例化一个 Cat
const kitty = new Cat({ name: 'Zildjian' })
// 持久化保存 kitty 实例
kitty.save().then(() => console.log('meow'))
```



## 3. 官方指南

### 3.1  设计 Schema  发布 Model

```js
const mongoose = require('mongoose')
const Schema = mongoose.Schema

// 1.连接数据库
mongoose.connect('mongodb://localhost:27017/itest')

// 2.设计集合结构（表结构）
// 字段名称就是表结构中的属性名称
// 约束的目的是为了保证数据的完整性, 不要有脏数据
var userSchema = new Schema({
    username:{
        type: String,
        required: true
    },
    password:{
        type: String,
        required: true
    },
    email:{
        type: String
    }
})

/*
*   3. 将文档结构发布为模型
*       mongoose,model 方法就是用来将一个结构发布为 model
*       第一个参数： 传入一个大写名词单数字符串用来表示你的数据库名称
*                   mongoose 会自动将大写名词的字符串生成 小写复数 的集合名称
*                   例如这里的 User 最终会变成 users 集合名称
*       第二个参数： 架构 Schema
*
*       返回值：模型构造函数
* */
var User = mongoose.model('User', userSchema)

// 4. 当我们有了模型构造函数之后 就可以使用这个构造函数对 User 中的数据 为所欲为（增删改查）
```



### 3.2 增加数据

```js
var admin = new User({
    username: 'admin',
    password: '12345',
    email: 'admin@admin.com'
})

admin.save().then(() => console.log('success'))
```



### 3.3 查询数据

查询所有

```js
User.find(function (err, res) {
    if (err) {
        console.log('查询失败')
    } else {
        console.log(res)
    }
})
```

按条件查询所有

```js
User.find({
    username: 'admin1'
},function (err, res) {
    if (err) {
        console.log('查询失败')
    } else {
        console.log(res)
    }
})
```

按条件查询单个

```js
User.findOne({
    username: 'admin1'
},function (err, res) {
    if (err) {
        console.log('查询失败')
    } else {
        console.log(res)
    }
})
```



### 3.4 删除数据

```js
User.deleteOne({username: 'admin'}, function (err) {
    if (err) {
        return handleError(err)
    } else {
        console.log('success')
    }
})

User.deleteOne({username: 'admin'}).then(() => {
    console.log('success')
})
```



### 3.5 更新数据



