---
title: "Golang: A Todo App using GIN"
date: 2020-12-30T02:01:58+05:30
description: "Gin is a web framework written in Go(Golang). If you need performance and good productivity, use Gin, you’ll love it."
tags: [Gin, Go, REST]
featured_image: "/images/gin/gin.png"
categories: Go
comment : false
---

Gin is a high-performance micro-framework that can be used to build web applications. It allows you to write middleware that can be plugged into one or more request handlers or groups of request handlers.

It is the most popular framework in Golang. Look at the picture above, this framework has the highest no. of stars on Github.com

## Goals
By the end of this tutorial, you will:

* Learn how to use Gin to create RESTful APIs (we will be creating a TODO application), and
* Understand the parts of a web application written in Go.

## Prerequisites
For this tutorial, you will need Go and postman installed on your machine.

__Note:__ If you don’t have postman, you can use any other tool that you would use to test API endpoints.

### List of Packages we are going to use:

```
go get github.com/go-sql-driver/mysql
go get github.com/gin-gonic/gin
go get github.com/jinzhu/gorm
```

### Project Structure:

![Project structure for GIN-TODO-APP](/images/gin/project-structure.png)

### List of APIs:

```
GET: /v1/todo 
POST: /v1/todo
GET: /v1/todo/:id
PUT: /v1/todo/:id
DELETE: /v1/todo/:id
```

Let’s start working, I would suggest you write each n every line of code by yourself, this way you’ll understand things better. So we’ll start by creating a project called GIN-TODO-APP.

## Setup Database
GORM is an ORM library written in Golang which we are using in our project. In this config file, we will be creating a function called __BuildDBConfig()__ where you can set up your DB host, port, username, password, and dbname and pass it to __DbURL()__ to create your database URL.

{{< gist ektagarg 6b3caeaee1b7f0194ce8790c3d21c6b3>}}

## Table and Schema
Let’s talk about DB Structure and schema. For this project, we’ll be having just one table ‘todo’. This table will have id, title, description as its attributes.

TableName() function will be used wherever we need to access the table name.

{{< gist ektagarg ea63891b05bad4134f98f375fffac830>}}

## Setup routes and list of APIs
Here, we are going to set up a router using

```
r := gin.Default()
```

Default returns a gin Engine instance with the Logger(write the logs to gin.DefaultWriter) and Recovery middleware already attached but if you need to start a blank gin instance with middleware attached, you should use gin.New()

```
v1 := r.Group("/v1")
```

You can group multiple routes using Group() function, these APIs will be accessible on /v1

{{< gist ektagarg eea34ca294af4c9d3a4005b7fd90e55d>}}

## Models: Database queries
Let’s write all the Database queries which we will be required to create APIs which includes: fetching all the records from table todo, insert a record, fetch a specific record, update an existing record, delete a record.

{{< gist ektagarg 8da3903a5422930b55d3f7384896fb30>}}

## Create an API: POST Todo
GIN provides methods(Must bind, Should bind) to bind request body into type which supports JSON, XML, YAML and form values.

```
c.BindJSON(&todo)
```

So here it will bind request body to __Models.Todo__ (when binding from JSON, set json:"fieldname") which is to:

```
type Todo struct {
     ID uint            `json:"id"`
     Title string       `json:"title"`
     Description string `json:"description"`
}
```

We can add validations to this struct as well, for eg: binding:”required” will return an error if it has empty value while binding.
Make a DB call to create a todo which is being handled by CreateATodo() written in Models/Todo.go. If it returns an error, call __AbortWithStatus__ which calls `Abort()` and writes the error code (We are using net/http package for __StatusNotFound__ and StatusOK codes).

POST API to create a TODO:

{{< gist ektagarg 84636e1253f5eb8d46ae1ba5ae80d447>}}

## RUN the server
Let’s get ready to combine DB config, Models, Routes, APIs into main.go. __gorm.Open()__ initialize a new DB connection:

```
db, err := gorm.Open("mysql", "user:password@/dbname?charset=utf8&parseTime=True&loc=Local")
```
__AutoMigrate()__ automatically migrates your schema, to keep your schema update to date.

One thing to note here is that AutoMigrate() will only create tables, missing columns and missing indexes, and won’t modify existing column’s data/type.

```
db.AutoMigrate(&Models.Todo{})
```

Setup the routes and run the server. Run() will listen and serve on 8080 by default.

```
r.Run()
```

main.go:
```
package main

import (
	"fmt"
	"./Config"
	"./Models"
	"./Routes"
	"github.com/jinzhu/gorm"
)

var err error

func main() {

	// Creating a connection to the database
	Config.DB, err = gorm.Open("mysql", Config.DbURL(Config.BuildDBConfig()))

	if err != nil {
		fmt.Println("statuse: ", err)
	}

	defer Config.DB.Close()

	// run the migrations: todo struct
	Config.DB.AutoMigrate(&Models.Todo{})

	//setup routes 
	r := Routes.SetupRouter()

	// running
	r.Run()
}
```

Perfect!! We are all set now. Let’s run this project:

```
go run main.go
```

And I can see an Engine instance has been created and the APIs are running:

![A running GO server](/images/gin/running-server.png)

Likewise, all are APIs are working as expected. If you find any kind of difficulty following the above steps,please check this [repo](https://github.com/ektagarg/gin-gorm-todo-app) and run:

```
git clone https://github.com/ektagarg/gin-gorm-todo-app
```




