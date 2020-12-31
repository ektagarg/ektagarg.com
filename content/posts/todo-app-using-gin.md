---
title: "Golang: A Todo App using GIN"
date: 2020-12-30T02:01:58+05:30
description: "Gin is a web framework written in Go(Golang). If you need performance and good productivity, use Gin, you’ll love it."
tags: [Gin, Golang, REST]
featured_image: "/images/gin/gin.png"
categories: Golang
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

```
package Config
import (
	"fmt"
	"github.com/jinzhu/gorm"
)

var DB *gorm.DB

// DBConfig represents db configuration
type DBConfig struct {
	Host     string
	Port     int
	User     string
	DBName   string
	Password string
}

func BuildDBConfig() *DBConfig {
	dbConfig := DBConfig{
		Host:     "0.0.0.0",
		Port:     3306,
		User:     "root",
		DBName:   "todos",
		Password: "mypassword",
	}
	return &dbConfig
}

func DbURL(dbConfig *DBConfig) string {
	return fmt.Sprintf(
		"%s:%s@tcp(%s:%d)/%s?charset=utf8&parseTime=True&loc=Local",
		dbConfig.User,
		dbConfig.Password,
		dbConfig.Host,
		dbConfig.Port,
		dbConfig.DBName,
	)
}
```

## Table and Schema
Let’s talk about DB Structure and schema. For this project, we’ll be having just one table ‘todo’. This table will have id, title, description as its attributes.

TableName() function will be used wherever we need to access the table name.

```
package Models

type Todo struct {
	ID uint            `json:"id"`
	Title string       `json:"title"`
	Description string `json:"description"`
}

func (b *Todo) TableName() string {
	return "todo"
}
```

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

```
package Routes

import (
	"../Controllers"
	"github.com/gin-gonic/gin"
)

func SetupRouter() *gin.Engine {
	r := gin.Default()
	v1 := r.Group("/v1")
	{
		v1.GET("todo", Controllers.GetTodos)
		v1.POST("todo", Controllers.CreateATodo)
		v1.GET("todo/:id", Controllers.GetATodo)
		v1.PUT("todo/:id", Controllers.UpdateATodo)
		v1.DELETE("todo/:id", Controllers.DeleteATodo)
	}
	return r
}
```

## Models: Database queries
Let’s write all the Database queries which we will be required to create APIs which includes: fetching all the records from table todo, insert a record, fetch a specific record, update an existing record, delete a record.
```
package Models

import (
	"../Config"
	"fmt"
	_ "github.com/go-sql-driver/mysql"
)

//fetch all todos at once
func GetAllTodos(todo *[]Todo) (err error) {
	if err = Config.DB.Find(todo).Error; err != nil {
		return err
	}
	return nil
}

//insert a todo
func CreateATodo(todo *Todo) (err error) {
	if err = Config.DB.Create(todo).Error; err != nil {
		return err
	}
	return nil
}

//fetch one todo
func GetATodo(todo *Todo, id string) (err error) {
	if err := Config.DB.Where("id = ?", id).First(todo).Error; err != nil {
		return err
	}
	return nil
}

//update a todo
func UpdateATodo(todo *Todo, id string) (err error) {
	fmt.Println(todo)
	Config.DB.Save(todo)
	return nil
}

//delete a todo
func DeleteATodo(todo *Todo, id string) (err error) {
	Config.DB.Where("id = ?", id).Delete(todo)
	return nil
}
```

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

```
func CreateATodo(c *gin.Context) {
	var todo Models.Todo
	c.BindJSON(&todo)
	// DB call to create a todo
	// Config.DB.Create(todo).Error;
	err := Models.CreateATodo(&todo)
	if err != nil {
		c.AbortWithStatus(http.StatusNotFound)
	} else {
		c.JSON(http.StatusOK, todo)
	}
}
```

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




