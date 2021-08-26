### 连接MongoDB数据库

使用 google 登录

1.创建PROJECT

第三步：

connect to your cluster:CONNECT

三种形式：第一种是命令 第二种是直接在项目中使用 第三种是一种压缩形式

**选择第二种**

![capture_20210523153853218](D:\Huawei Share\Screenshot\capture_20210523153853218.bmp)

![capture_20210523153939806](D:\Huawei Share\Screenshot\capture_20210523153939806.bmp)

![image-20210523154057297](C:\Users\hhy\AppData\Roaming\Typora\typora-user-images\image-20210523154057297.png)

**hhy123123** ·password

mongodb+srv://hhy:<password>@cluster0.rjhk0.mongodb.net/myFirstDatabase?retryWrites=true&w=majority

```
const mongoose = require('mongoose')

mongoose
.connect('mongodb+srv://hhy:xxxxx@cluster0.rjhk0.mongodb.net/myFirstDatabase?retryWrites=true&w=majority',{ useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => { console.log('mongodb connected') })
  .catch((err)=>{console.log(err)})
```



### 创建路由和用户模型

- routes/users.js

```
// @ login & regist

const express = require('express')

const router = express.Router();

// $route GET api/users/test
// @desc 返回请求的 json 数据
// @access public
// http://localhost:3000/api/users/test
router.get("/test", (req, res) => {
  res.json({msg:'login works'})
})

module.exports = router
```

使用：

server.js

```
// 引入 user.js
const users = require('./routes/api/users')

// 使用 routes
app.use("/api/users",users)
```

http://localhost:3000/api/users/test 可以看见输出



> module.exports & default.exports 有什么区别

> require & import 有什么区别？

> schema 是什么？



### 搭建注册接口并存储数据

// 报错：'bodyParser' is deprecated.

![image-20210523164304482](C:\Users\hhy\AppData\Roaming\Typora\typora-user-images\image-20210523164304482.png)

Hhymongodb5120

1. router post API /register in user.js 
2. in post method use User modal 首先判断是否存在邮箱(findOne方法) 如果存在返回400错误 如果没有传进request中填的user信息 
3. 处理password加密 使用bcrypt加密 hash值即为password
4. 调用newUser.save()保存到数据库中

> Q：body-parser 中间件有什么用？



### 搭建登录接口  使用jwt实现token

1. router post api /login in user.js

2. in post   method use User modal 首先判断是否存在邮箱(findOne方法) 如果存在返回400错误

3. 使用 bcrypt.compare 校验密码是否正确 若不正确返回404

4. jwt.sign("规则","加密名字","过期时间","箭头函数",)  npm 中有

   

### 验证token

1.验证token需要用 passport && passport-jwt

```
npm i passport passport-jwt --save
```

2.需要初始化passport

```
const passport = require('passport')

app.use(passport.initialize())
// 后面的 passport 可以作为参数
require('./config/passport')(passport)
```

3. passport.js

```
// 使用方法里有
const JwtStrategy = require('passport-jwt').Strategy,
  ExtractJwt = require('passport-jwt').ExtractJwt;

const mongoose = require('mongoose')
const User = mongoose.model("users")
const keys = require("../config/keys")

const opts = {}
opts.jwtFromRequest = ExtractJwt.fromAuthHeaderAsBearerToken();
opts.secretOrKey = keys.SecretOrKey


// passport 是 server.js 中引进来的
module.exports = passport => {
  passport.use(new JwtStrategy(opts, (jwt_payload, done) => {
    // 打印： { id: '60aa593725bcf53a64275edb', iat: 1621858533, exp: 1621862133 }
    console.log(jwt_payload)
    User.findById(jwt_payload.id)
      .then(user => {
        if (user) {
           return done(null,user);
        }
        return done(null,false)
      }).catch(err => console.log(err))
  }))
}


```

4. user.js中

```
const passport = require('passport')

// $route GET api/users/current
// @desc return current user
// @access private
router.get("/current", passport.authenticate("jwt", { session: false }), (req, res) => {
  
  // 因为 passport.js中 成功返回 user
  res.json({
    id: req.user.id,
    username: req.user.username,
    email: req.user.email,
    avatar: req.user.avatar
  })
})
```



### 使用 validator 验证用户信息

1. 使用validator 第三方模块

   如 isEmail isEmpty

2. 新建validation 文件夹

   - is-empty

     ```
     const isEmpty = value => {
       return
         value === undefined || value == null || (typeof value === 'object' && Object.keys(value).length === 0) || (typeof value === 'string' && value.trim().length === 0)
     }
     
     module.exports = isEmpty
     ```

     

   - login

     // 省略

   - regitser

     ```
     const Validator = require("validator")const isEmpty = require('./is-empty')module.exports = function validateRegisterInput(data) {  const errors = {  }  if (!Validator.isLength(data.username, { min: 2, max: 30 })) {    errors.name = '名字的长度不能小于2位并且不能大于30位！';  }  return {    errors,    isValid:isEmpty(errors)  }}
     ```

     

   3. user.js 中的 /register

      ```
      const { errors, isValid } = validateRegisterInput(req.body)  if (!isValid) {    return res.status(400).json(errors)  }
      ```

      （包含 login 的）