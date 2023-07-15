+++
author = "Harsh"
title = "HTTP Protocol: Where we started, where we are, and how we got here"
date = "2023-02-15"
description = "HTTP Protocol history"
tags = [
    "web",
    "internet",
    "HTTP",
]
+++

HTTP, or Hypertext Transfer Protocol, is the standard protocol for sending and receiving data over the internet. HTTP establishes the guidelines for information exchange and communication between clients and servers. <br>

In this post, we will explore different versions of HTTP, their features, use cases and everything in between. <br>
HTTPS is intentionally left out from the discussion to keep the focus on understanding the core of HTTP protocol without adding complications. <br>

## HTTP

HTTP is a protocol that specifies how data should be formatted and transmitted over the web I.E. how web servers and browsers should communicate and exchange data. <br>
HTTP is a **request-response** protocol, meaning that a client (such as a web browser) sends a request to a server (such as a web server), and the server responds to the client with a response. The response typically consists of a status code and the requested data (such as an HTML page or JSON data). <br>
HTTP is a **stateless protocol**, meaning that it does not maintain a connection between requests; each request is treated as an independent transaction, and the server does not store any information about previous requests. <br>

**Let's walk through an example of a HTTP request and response.** <br>
Imagine that you are using your web browser to visit the website `www.example.com`. When you type this URL into your browser and hit enter, your browser sends a HTTP **GET** request to the server at `www.example.com` asking for the homepage.
The server at `www.example.com` receives the request and looks for the homepage file (usually index.html). If the file is found, the server sends back a HTTP response with a **200 OK** status code, along with the contents of the index.html file. Your web browser receives this response and displays the homepage for `www.example.com` on your screen.

This is a very simplified example, much more goes on behind the scenes but, this illustrates HTTP request and response at a high level.

### HTTP 1.0

The first version of the HTTP protocol, HTTP 1.0 was released in 1996. <br>
HTTP 1.0 introduced the core principles of HTTP, including the request/response model, the URI (Uniform Resource Identifier) syntax, and the use of headers to provide metadata about the request and response. <br>

The most prominent shortcoming of HTTP 1.0 is it’s reliance on a **connection-oriented** model. In a connection oriented model, a new connection must be established for every request/response pair. This can be inefficient for most modern applications that require multiple requests to be sent to the server.<br>

On top of this, HTTP 1.0 **does not support** **caching**. Web browsers must request a new copy of a webpage from the server each time it is accessed. This can be slow and inefficient, especially for web pages that contain a large amount of static content.

### HTTP 1.1

HTTP 1.1 was released in January 1997, only a few months after HTTP 1.0, and addressed the major drawbacks of HTTP 1.0. <br>

HTTP 1.1 utilizes a **persistent connection** model, which allows multiple requests to be sent over a single, persistent connection. This is more efficient than the connection-oriented model used by HTTP 1.0 because it eliminates the need to establish a new TCP connection for each request. <br>

HTTP 1.1 also introduced **support for caching**, allowing HTTP responses to be stored locally. This meant that the subsequent requests for the same content could be served more quickly from the cache instead of requesting the content from the server. <br>

HTTP 1.1 went further than just persistent connection and caching, and also introduced the concept of **conditional requests**. Conditional requests let a client include one or more conditional headers in an HTTP request, such as "If-Modified-Since" or "If-Match" and the server can then evaluate these conditions and only respond if the conditions are met. For example, a client can use the _IF-MODIFIED-SINCE_ header to retrieve a new copy of a resource only if it has been modified since the last time it was accessed. This can be more efficient than sending a full response when the client already has the latest version of the resource. Conditional requests are often used in **HTTP caching**, where a client can use a conditional request to check if the server has a newer version of a resource that the client has previously cached. <br>

### HTTP/2

The third iteration of the HTTP protocol, HTTP/2, was introduced in 2015. It is a significant improvement to HTTP 1.1 and includes several new features and enhancements that improve performance and efficiency. <br>

One of the primary advantages of HTTP/2 over HTTP 1.1 is that it employs a **binary framing layer**, which along with **multiplexing** allows multiple requests and responses to be sent over a single connection simultaneously. HTTP/2 achieve this by breaking down the data being transmitted into small, individual frames that can be easily transmitted over the network. <br>
Together, multiplexing and binary framing allow HTTP/2 to more efficiently transmit data over the web.

HTTP/2 also includes **header compression**, which reduces the size of HTTP headers and the amount of data that must be transmitted across the network. This is achieved by encoding common header fields in a more compact format. <br>
HTTP 1.1 sends each header field as a **separate key-value pair**, which can be redundant and result in a large amount of data being transmitted. Header compression in HTTP/2 **maps common header fields** to a **unique identifier**, which is then substituted for the entire header field.
This has the potential to significantly improve performance, particularly for applications that make a large number of requests. <br>

Additionally, HTTP/2 supports **server push**, which allows the server to proactively send additional resources to the client without the client having to request them. For example, if a client requests an HTML page, the server can send the client any additional resources required to render the page, such as stylesheets or fonts. This can improve web page performance by reducing the number of round trips and the amount of data that must be transferred.

> Server Push is optional and is usually disabled by default.

### HTTP/3

HTTP/3 is the fourth and latest version of the HTTP protocol. It is an updated and improved version of HTTP/2 and offers several new features and enhancements. <br>

The primary improvement in HTTP/3 over HTTP/2 is the use of a new transport layer protocol **QUIC**. <br>

QUIC (Quick UDP Internet Connections) is a protocol based on **UDP** and it aims to address some of the challenges of using TCP for high-speed, low-latency applications, such as online gaming. <br>
QUIC is a faster and more efficient protocol designed to reduce the overhead of establishing and maintaining a connection which reduces the number of round trips required for a request/response cycle which in turn can improve performance and reduce latency. It does this through faster connection establishment, improved congestion control, and better support for multiplexing multiple streams of data over a single connection. This makes it faster and more efficient than TCP. <br>

HTTP/3 also adds support for **TLS 1.3 encryption**, which provides greater security and privacy than previous TLS versions. This enables HTTP/3 to provide improved security against man-in-the-middle attacks and other security threats. <br>

Additionally, armed with QUIC, HTTP/3 is designed to be more flexible and adaptable to changing network conditions, which can improve the performance and reliability of web applications, especially on mobile devices and other networks with high latency or other challenges. <br>

HTTP 3 is still in the early stages of adoption, but it is already being supported by [75% of web browsers and 25% of the top 10 million websites](https://en.wikipedia.org/wiki/HTTP/3#:~:text=HTTP%2F3%20is%20supported%20by,Mozilla%20Firefox%20since%20May%202021.) including Google and Facebook.
