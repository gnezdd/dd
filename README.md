# dd
该项目是参考gin实现的一个简易web框架，支持以下功能：
- 路由(Routing)：将请求映射到函数，基于前缀树实现，支持动态路由、分组控制。例如'/hello/:name
- 模板(Templates)：使用内置模板引擎提供模板渲染机制
- 中间件(middlewares)：提供插口，允许用户自己定义功能，嵌入到框架中
- 错误恢复(recover)：错误处理机制中间件，支持崩溃恢复
- ...

## 运行示例
**项目结构：**
```
|--dd/
|--static/
    |--css/
        |--dd.css
    |--file1.txt
|--templates/
    |--arr.tmpl
    |--css.tmpl
    |--custom_func.tmpl
|--go.mod
|--main.go
```
**go.mod：**
```
module example

go 1.18

require (
	dd v0.0.0
)

replace (
	dd => ./dd
)
```

**main.go：**
```go
package main

import (
	"dd"
	"fmt"
	"log"
	"net/http"
	"time"
)

func indexHandler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintf(w, "URL.Path = %q\n", r.URL.Path)
}

func helloHandler(w http.ResponseWriter, r *http.Request) {
	for k, v := range r.Header {
		fmt.Fprintf(w, "Header[%q] = %q\n", k, v)
	}
}

/*
type Engine struct {
}

func (engine *Engine) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	switch r.URL.Path {
	case "/":
		fmt.Fprintf(w, "URL.Path = %q\n", r.URL.Path)
	case "/hello":
		for k, v := range r.Header {
			fmt.Fprintf(w, "Header[%q] = %q\n", k, v)
		}
	default:
		fmt.Fprintf(w, "404 not found: %s\n", r.URL)
	}
}
*/

func onlyForV2() dd.HandlerFunc {
	return func(c *dd.Context) {
		t := time.Now()
		c.Fail(500, "Internal Server Error")
		log.Printf("[%d] %s in %v for group v2", c.StatusCode, c.Req.RequestURI, time.Since(t))
	}
}

type student struct {
	Name string
	Age  int8
}

func FormatAsDate(t time.Time) string {
	year, month, day := t.Date()
	return fmt.Sprintf("%d-%02d-%02d", year, month, day)
}

func main() {
	// 映射路由
	//http.HandleFunc("/", indexHandler)
	//http.HandleFunc("/hello", helloHandler)
	//log.Fatal(http.ListenAndServe(":8080", nil))

	//engine := new(Engine)
	//log.Fatal(http.ListenAndServe(":8080", engine))

	//server := dd.New()

	//server.GET("/", indexHandler)
	//server.GET("/hello", helloHandler)

	//server.GET("/hello", func(c *dd.Context) {
	//	c.String(http.StatusOK, "hello %s, you are at %s\n", c.Query("name"), c.Path)
	//})
	//server.POST("/login", func(c *dd.Context) {
	//	c.JSON(http.StatusOK, dd.H{
	//		"userName": c.PostForm("userName"),
	//		"password": c.PostForm("password"),
	//	})
	//})
	//server.GET("/hello/:name", func(c *dd.Context) {
	//	c.String(http.StatusOK, "hello %s, you are at %s\n", c.Param("name"), c.Path)
	//})
	//server.GET("/assets/*filepath", func(c *dd.Context) {
	//	c.JSON(http.StatusOK, dd.H{
	//		"filepath": c.Param("filepath"),
	//	})
	//})
	//
	//v1 := server.Group("/v1")
	//{
	//	v1.GET("/", func(c *dd.Context) {
	//		c.HTML(http.StatusOK, "<h1>hello v1/</h1>")
	//	})
	//	v1.GET("/hello", func(c *dd.Context) {
	//		c.String(http.StatusOK, "hello %s, you are at %s\n", c.Query("name"), c.Path)
	//	})
	//}

	//server.Use(dd.Logger())
	//
	//v2 := server.Group("/v2")
	//v2.Use(onlyForV2())
	//{
	//	v2.GET("/hello/:name", func(c *dd.Context) {
	//		c.String(http.StatusOK, "hello %s, you are at %s\n", c.Param("name"), c.Path)
	//	})
	//}

	//server.Use(dd.Logger())
	//server.SetFuncMap(template.FuncMap{
	//	"FormatAsDate": FormatAsDate,
	//})
	//server.LoadHTMLGlob("templates/*")
	//server.Static("/assets", "./static")
	//
	//stu1 := &student{Name: "dd", Age: 20}
	//stu2 := &student{Name: "cc", Age: 10}
	//server.GET("/", func(ctx *dd.Context) {
	//	ctx.HTML(http.StatusOK, "css.tmpl", nil)
	//})
	//server.GET("/students", func(ctx *dd.Context) {
	//	ctx.HTML(http.StatusOK, "arr.tmpl", dd.H{
	//		"title":  "dd",
	//		"stuArr": [2]*student{stu1, stu2},
	//	})
	//})
	//
	//server.GET("/date", func(ctx *dd.Context) {
	//	ctx.HTML(http.StatusOK, "custom_func.tmpl", dd.H{
	//		"title": "dd",
	//		"now":   time.Date(2023, 5, 26, 0, 0, 0, 0, time.UTC),
	//	})
	//})

	server := dd.Default()
	server.GET("/", func(ctx *dd.Context) {
		ctx.String(http.StatusOK, "Hello dd\n")
	})
	server.GET("/panic", func(ctx *dd.Context) {
		names := []string{"dd"}
		ctx.String(http.StatusOK, names[100])
	})

	server.Run(":8080")
}
```




## 学习参考资料：
- [7天用Go从零实现Web框架Gee教程](https://geektutu.com/post/gee.html)
- [Gin文档](https://gin-gonic.com/zh-cn/)

