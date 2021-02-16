---
title: "Improve your web performance by switching from HTTP 1.1 to HTTP 2.0"
date: 2021-02-16T02:01:58+05:30
description: ""
tags: [Networking]
featured_image: "/images/http2/http2-top.png"
categories: Networking
comment : false
---
HTTP 2.0 is the improvement in HTTP 1.0 which was introduced late in 2015. To understand HTTP 2.0 better, let's first deep dive into the history of HTTP.

## History of HTTP

![History of HTTP](/images/http2/HTTP2.png)

### HTTP 0.9 
HTTP 0.9 was introduced in 1991. At that time most of the websites were having basic text-based content hyperlinked to each other. It was working as its name suggests - Hyper Text Transfer Protocol.

- Basic Operations GET, POST, HEAD were supported. 
- Its purpose was to serve basic websites hyperlinked with each other.

### HTTP 1.0
After 5 years, HTTP 1.0 was introduced with a few other basic operations like PUT, DELETE, LINK, and UNLINK methods.

### HTTP 1.1
Immediately after 1 year, in 1997, HTTP 1.1 was introduced. This is the current HTTP protocol which we are still using.

- HTTP 1.1 was introduced with few changes because that was the internet revolution phase, the internet ecosystem was rapidly growing. Websites like an email client, search engines, social media giants were getting created with heavily loaded dynamic content. Most platforms had high usage of images, interactive content, more features that required high resource usage.

A lot of things were introduced to make the internet fast and allowing users to access the server faster.
 
- Support of OPTIONS
- CORS, Keep-alive was part of this version of HTTP

## But what was not there in HTTP 1.1?

In HTTP 1.1 for achieving speed on the website, if you are already a web developer you must be knowing these, that we are advised to do compression, caching, minifying CSS/JS, image sprites for icons, and another resource because the TCP connection used between the client and server are limited.

#### 1. HOL- Head of Line
Whenever a client requests a URL, an HTTP request is sent via a TCP connection, which is created between the client and the server. Initially, there was only a single TCP connection.

Let's understand this with an example:

If a user requests for say, home.html, which we know for sure that internally requires a CSS and a JS file. So the first request will be created for the home.html file, as there is a single TCP connection, it will wait for the response of that home.html i.e. TCP connection stays blocked until it receives a response. and after it receives home.html, the next request will be sent for CSS file and then next would be for JS file in a similar manner.

Later on, this was increased to 6 parallel TCP connections, but these days websites are at a much larger scale and even 6 TCP connections are not enough.

#### 2. Repetition of Header Data
HTTP is a stateless connection, which means every request is independent of the previous and the next request, so each request has to contain request header data.

```
Request Header:
user-agent:xxx
cookie:xxx
cache-control:xxx
```

The same header is packed with all the requests and responses and it is useless sometimes when it is precisely same in all the requests. Header data can not be compressed, while you can compress the body of the request.

# How come HTTP 2.0 going to solve all the problems?

![HTTP 2.0](/images/http2/http-2-file.png)

### HTTP 2.0

1. __Single Secured TCP Connection__: This is the solution to problem 1: HOL- Head of Line

- In HTTP 2.0, a single secured TCP connection has been used, in which streams are created.
Each HTTP request will be sent into streams over a single secured TCP connection.

- It is faster, as you are not setting up multiple TCP connections here, and the client is managing the streams which are multiplexing of streams into a single TCP connection.

NOTE: It is mandatory to have TLS setup i.e. HTTPS for HTTP2

2. __HPACK__: This is the solution to problem 2: Repetition of Header Data

- Header data is separated from basic request data. It allows compression of header data.

- If the preceding request had some header data, HPACK automatically enables the caching of that header data. Each request will not contain the same header data repeatedly which will make request data and delivery of data much faster on websites.

3. __PUSH__: PUSH frames enable us to send mandatory resources along with the HTTP response. 

For eg: You request a home.html page from a website and you know that to show this page on the browser, you must need a CSS and a JS file.
You can make your request in such a way that server will send home.html along with the required CSS and JS file.

- PUSH frames should be used with care as they can increase the size of the HTTP response.

4. Along with all this, we can keep on using gzip, browser caching, minifying CSS/JS to further improve the performance.

5. Almost all of the modern browsers support HTTP 2.0

If you are moving from HTTP 1.1 to HTTP 2.0, the users using old browsers which are typically using HTTP 1.1 will be served via HTTP 1.1 only as HTTP 2.0 is built on top of HTTP 1.1

With more and more adoption of HTTP 2.0, all of the modern servers are becoming compliant to HTTP 2.0 If you are looking for improving the performance of your website, you should definitely move from HTTP 1.1 to HTTP 2.0