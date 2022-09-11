## Gin 框架



#### 函数：

**Query:**

```go
func (c *Context) Query(key string) string {
	value, _ := c.GetQuery(key)
	return value
}
```

Query返回参数值，如果不存在的话则返回空字符串""









```
package main

import (
   "github.com/gin-gonic/gin"
)

//
func sayHello(c *gin.Context) {
   c.JSON(200, gin.H{
      "message": "hello golang!",
   })
}
func main() {
   // 1.创建默认路由引擎
   r := gin.Default()
   // 2.绑定路由规则，执行的函数
   // gin.Context，封装了request和response
   // GET：请求方式；/hello：请求的路径
   r.GET("/hello", sayHello)

   //启动服务
   r.Run(":8080")
}
```

### RESTful API

简单来说，REST的含义就是客户端与Web服务器之间进行交互的时候，使用HTTP协议中的4个请求方法代表不同的动作。

- `GET`用来获取资源
- `POST`用来新建资源
- `PUT`用来更新资源
- `DELETE`用来删除资源。

### gin 获取URI 路径参数

```go
func main() {
	r := gin.Default()

	r.GET("/:name/:age", func(c *gin.Context) {
		name := c.Param("name")
		age := c.Param("age")
		c.JSON(http.StatusOK, gin.H{
			"name": name,
			"age":  age,
		})
	})
	r.GET("/blog/:year/:month",func (c *gin.Context)  {
		year := c.Params("year")
		month := c.Params("month")
		c.JSON(http.StatusOK, gin.H{
			"year" : year,
			"month" : month,
		})
	})
	r.Run()
}

```

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220514165939531.png" alt="image-20220514165939531" style="zoom:80%;" />





c.Get获取上下文绑定键值，通过strconv.Atoi转换为int

```go
userId, exist := c.Get("username")
strconv.Atoi(userId.(string))
```





### gin获取querystring参数

应用场景：搜索引擎进行搜索关键字

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220621235430563.png" alt="image-20220621235430563" style="zoom:67%;" />



格式：`?name=123&id=123`  。路由后加`？`，各键值对用`&`连接

#### Query函数

获取请求中携带的参数

函数原型：

`func (c *Context) Query(key string) (value string)`

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220622000941626.png" alt="image-20220622000941626" style="zoom:80%;" />



```go
func main() {
	r := gin.Default()
	r.GET("/index", func(c *gin.Context) {
		//获取querystring参数
		name := c.Query("name")
		id := c.Query("id")
		c.JSON(http.StatusOK, gin.H{
			"name": name,
			"id":   id,
		})
	})
	r.Run()
}
```



**DefaultQuery**

设置默认值，若没有查找到边添加默认值。

`func (c *Context) DefaultQuery(key string, defaultValue string) string`


```go
		name := c.DefaultQuery("name", "test")
		id := c.DefaultQuery("id", "0")
```



<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220622002031785.png" alt="image-20220622002031785" style="zoom:80%;" />



**GetQuery**

`func (c *Context) GetQuery(key string) (string, bool)`

搜索到参数还将返回布尔值，搜索到 true，未搜索到 false



#### gin.Context.Query

func (*gin.Context).Query(key string) string



<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220620151207002.png" alt="image-20220620151207002" style="zoom:67%;" />



<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220620151229726.png" alt="image-20220620151229726" style="zoom:67%;" />

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220620151245240.png" alt="image-20220620151245240" style="zoom:67%;" />

QueryCache存放在context结构体内，使用url.ParseQuery缓存参数查询结果

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220620151733390.png" alt="image-20220620151733390" style="zoom:67%;" />

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220620151404465.png" alt="image-20220620151404465" style="zoom:67%;" />





### 参数绑定

`ShouldBind`会按照下面的顺序解析请求中的数据完成绑定：

1. 如果是 `GET` 请求，只使用 `Form` 绑定引擎（`query`）。
2. 如果是 `POST` 请求，首先检查 `content-type` 是否为 `JSON` 或 `XML`，然后再使用 `Form`（`form-data`）

```
package main

import (
	"fmt"
	"net/http"

	"github.com/gin-gonic/gin"
)

type UserInfo struct {
	//大写 外部可访问
	//form 从c.Context 中取出对应的绑定参数
	Username string `form:"username" json:"username" `
	Password string `form:"password" json:"password" `
}

func main() {
	r := gin.Default()

	r.GET("/user", func(c *gin.Context) {
		// username := c.Query("username")
		// password := c.Query("password")
		// u := UserInfo{
		// 	username: username,
		// 	password: password,
		// }
		var u UserInfo //声明UserInfo 类型变量u

		//请求相关数据绑定
		//ShouldBind检查Content-Type以自动选择绑定引擎，
		//具体取决于“Content-Type”标头使用不同的绑定：

		//****************ShuldBind***********************************
		err := c.ShouldBind(&u) //使用指针 传递指针才能修改
		if err != nil {
			c.JSON(http.StatusBadRequest, gin.H{
				"error": err.Error(),
			})
		} else {
			//fmt.Println("%#v\n", u)
			c.JSON(http.StatusOK, gin.H{
				"status": "ok",
			})
		}
	})
	//******************json*************************************
	r.POST("/json", func(c *gin.Context) {
		var u UserInfo          //声明UserInfo 类型变量u
		err := c.ShouldBind(&u) //使用指针 传递指针才能修改

		if err != nil {
			c.JSON(http.StatusBadRequest, gin.H{
				"error": err.Error(),
			})
		} else {
			fmt.Println("json: \n", u)
			c.JSON(http.StatusOK, gin.H{
				// "username": u.Username,
				// "password": u.Password,
				"status": "ok",
			})
		}
	})

	//***************form*************************************
	//post 新建资源
	r.POST("/form", func(c *gin.Context) {
		var u UserInfo          //声明UserInfo 类型变量u
		err := c.ShouldBind(&u) //使用指针 传递指针才能修改
		if err != nil {
			c.JSON(http.StatusBadRequest, gin.H{
				"error": err.Error(),
			})
		} else {
			//fmt.Println("%#v\n", u)
			c.JSON(http.StatusOK, gin.H{
				"status": "ok",
			})
		}
	})

	r.Run()
}

```



使用postman 发送post请求 **form-data 数据**

![image-20220514182734984](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220514182734984.png)





使用postman 发送post ， **json 格式数据**

![image-20220514183428969](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220514183428969.png)





### 文件上传

上传文件就是处理了对方的post请求





















### gin 请求重定向

![image-20220518105256446](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220518105256446.png)



### 路由和路由组

路由组：

`func (group *RouterGroup) Group(relativePath string, handlers ...HandlerFunc) *RouterGroup`



```go
func main() {
	r := gin.Default()

	//访问index的GET请求会由以下匿名函数处理
	r.GET("/index", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"method": "GET",
		})
	})
	//访问index的post请求会由以下匿名函数处理
	r.POST("/index", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"method": "post",
		})
	})
	//访问index的put请求会由以下匿名函数处理
	r.PUT("/index", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"method": "put",
		})
	})
	//访问index的delete请求会由以下匿名函数处理
	r.DELETE("/index", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{
			"method": "delete",
		})
	})
	//使用any处理所有的请求
	r.Any("/user", func(c *gin.Context) {
		switch c.Request.Method {
		case "GET":
			c.JSON(http.StatusOK, gin.H{"method": "GET"})
		case http.MethodPost:
			c.JSON(http.StatusOK, gin.H{"method": "POST"})
			//...
		}
	})
	//NoRoute 访问没有处理的定义的路由由以下逻辑进行处理
	r.NoRoute(func(c *gin.Context) {
		c.JSON(http.StatusNotFound, gin.H{})
	})

	//路由的组 路由组
	userGroup := r.Group("/video")
	userGroup.GET("/index", func(c *gin.Context) {c.JSON(http.StatusOK, gin.H{"method": "GET"})})
	userGroup.GET("/login", func(c *gin.Context) {c.JSON(http.StatusOK, gin.H{"method": "GET"})})
	userGroup.POST("/login", func(c *gin.Context) {c.JSON(http.StatusOK, gin.H{"method": "POST"})})
	
	
	_ = r.Run()
}
```



### Gin中间件

中间件适合处理公共的业务逻辑，例如登录校验，权限校验。

允许开发者在处理请求的过程中，加入用户自己的钩子（HOOK）函数 

Gin中的中间件必须是一个`gin.HandlerFunc`（包含请求上下文的函数）类型。

 **gin默认中间件**

`gin.Default()`默认使用了`Logger`和`Recovery`中间件，其中：

- `Logger`中间件将日志写入`gin.DefaultWriter`，即使配置了`GIN_MODE=release`。
- `Recovery`中间件会recover任何`panic`。如果有panic的话，会写入500响应码。

如果不想使用上面两个默认的中间件，可以使用`gin.New()`新建一个没有任何默认中间件的路由。

#### 注册中间件

1. **全局路由注册**

`func (engine *Engine) Use(middleware ...HandlerFunc) IRoutes`

全局路由注册后，所有路由都将先依次执行全局中间件函数

2. **单独注册中间件**

在GET函数中注册：

GET函数可以接收不定数量的参数，第二参数是` handlers ...HandlerFunc`,

```
r.GET("/index", m1Handler, m2Handler, indexHandler)
```

中间件将顺序依次执行。

3. **为路由组注册中间件**

   ​	第一种写法：

```go
	cocoGroup := r.Group("/coco")
	{
		cocoGroup.GET("/index", m2Handler, indexHandler)
		cocoGroup.GET("/hello", indexHandler)
	}
```

第二种写法：

```go
	cocoGroup.Use(m1Handler)
	{
		cocoGroup.GET("/index", m2Handler, indexHandler)
		cocoGroup.GET("/hello", indexHandler)
	}
```







### 小项目——任务清单

/#/ vue默认使用hash模式

![image-20220521174128393](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220521174128393.png)



可以在进入之前创造一个用户登陆注册页面，根据不同的用户返回不同的表单





![image-20220521172936946](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220521172936946.png)

添加任务时，post请求指向 /v1/todo

![image-20220521173003350](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220521173003350.png)





#### 后台运行部署nohup

![image-20220728145821528](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220728145821528.png)







