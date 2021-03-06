# g4vercel-demo
 
> Deploy go web server in vercel

The Go Runtime is used by Vercel to compile Go Serverless Functions that expose a single HTTP handler, from a `.go` file within an `/api` directory at your project's root.


## Example

For example, define an `index.go` file inside an `/api` directory as follows:

```go

package handler

import (
	"fmt"
	"net/http"

	. "github.com/tbxark/g4vercel"
)

func Handler(w http.ResponseWriter, r *http.Request) {
	server := New()
	server.Use(Recovery(func(err interface{}, c *Context) {
		if httpError, ok := err.(HttpError); ok {
			c.JSON(httpError.Status, H{
				"message": httpError.Error(),
			})
		} else {
			message := fmt.Sprintf("%s", err)
			c.JSON(500, H{
				"message": message,
			})
		}
	}))
	server.GET("/", func(context *Context) {
		context.JSON(200, H{
			"message": "OK",
		})
	})
	server.GET("/hello", func(context *Context) {
		name := context.Query("name")
		if name == "" {
			context.JSON(400, H{
				"message": "name not found",
			})
		} else {
			context.JSON(200, H{
				"data": fmt.Sprintf("Hello %s!", name),
			})
		}
	})
	server.GET("/user/:id", func(context *Context) {
		context.JSON(400, H{
			"data": H{
				"id": context.Param("id"),
			},
		})
	})
	server.GET("/long/long/long/path/*test", func(context *Context) {
		context.JSON(200, H{
			"data": H{
				"url": context.Path,
			},
		})
	})
	server.Handle(w, r)
}

```

An example `index.go` file inside an `/api` directory.



## Config

You must add `vercel.json` to forward all path requests to `api/index.go`, then you can use code to control routing, otherwise you will use vercel default routing forwarding

```json
{
  "routes": [
    { "src": "/(.*)", "dest": "/api" }
  ]
}
```
