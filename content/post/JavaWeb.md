---
title: JavaWeb学习笔记
date: 2025-04-01
draft: false
---

# JavaWeb 学习笔记

[跟学视频 尚硅谷 JavaWeb 2023-10-25](https://www.bilibili.com/video/BV1UN411x7xe/?spm_id_from=333.1387.upload.video_card.click)

因个人偏好，暂时跳过 html css js 三件套。

## Tomcat

### 简介

* 为 **Java Servlet** 和 **JSP** 提供运行环境。
* 是一个轻量级的 Web 服务器。
* 是 Java Web 应用的标准部署容器。



## HTTP

### 简介

#### 基本概念

* HTTP 是用于传输超文本的应用层写译
* 其特点为：
  * **无状态**：服务器不记录其客户端状态（依赖 cookie/session 解决）
  * **基于请求-响应模型**：客户端发起请求，服务器返回响应。
  * **可拓展**：通过 Headers 和 body 灵活传递数据。核心术语

### 请求与响应结构

* 请求格式

  ```http
  GET /path?param = value HTTP/1.1
  Host: example.com
  User-Agent: Mozilla/5.0
  Accept: text/html
  
  假设请求地址为http://127.0.0.1:8080//userservlet
  请求内容为username = zhangsan
  
  Get直接将参数放入url: 
  http://127.0.0.1:8080//userservlet?username=zhangsan
  
  Post会将参数放入请求体:
  [请求体]
  username = zhangsan
  ```

* **常用请求方式**

  | 方法   | 用途                 |
  | :----- | :------------------- |
  | GET    | 获取资源             |
  | POST   | 提交数据             |
  | PUT    | 更新资源             |
  | DELETE | 删除资源             |
  | HEAD   | 获取头信息（无 body） |

* 响应格式

  ```html
  HTTP/1.1 200 OK
  Content-Type: text/html	
  Content-Length: 1234
  
  <!DOCTYPE html>
  <html>...</html>
  ```

* **常用响应码**

  | 状态码 | 类别       | 说明           |
  | ------ | ---------- | -------------- |
  | 200    | 成功       | 请求成功       |
  | 301    | 重定向     | 资源永久移动   |
  | 404    | 客户端错误 | 资源不存在     |
  | 500    | 服务器错误 | 服务器内部错误 |

## Servlet

### **Servlet 简介**

#### **定义**

- **Servlet** 是 Java 编写的**服务器端程序**，用于处理客户端的 HTTP 请求并生成动态响应。
- 运行在 **Servlet 容器**（如 Tomcat、Jetty）中，容器负责管理其生命周期和网络通信。

#### **核心作用**

**承前启后**

- **接收请求**：解析 HTTP 请求（参数、Headers 等）。
- **处理业务**：执行 Java 代码逻辑。
- **生成响应**：返回 HTML、JSON 或其他格式的数据。

####  **主要特性**

- **平台无关**：基于 Java，跨操作系统。
- **高效性**：多线程处理请求（每个请求由独立线程处理）。
- **可扩展性**：可通过 Filter、Listener 增强功能。



### **Servlet 运行流程**

![image-20250401114634368](https://cdn.jsdelivr.net/gh/Planetesimals-2005/BlogImg/img/image-20250401114634368.png)

![QQ20250401-114007](https://cdn.jsdelivr.net/gh/Planetesimals-2005/BlogImg/img/QQ20250401-114007.png)

![image-20250401114845361](https://cdn.jsdelivr.net/gh/Planetesimals-2005/BlogImg/img/image-20250401114845361.png)

**详细步骤**

1. **客户端发起请求**
   浏览器通过 URL 发起 HTTP 请求。
2. **容器接收请求**
   Servlet 容器监听到请求，解析 URL 确定目标 Servlet。
3. **创建 Request/Response 对象**
   - 容器封装 HTTP 请求数据为 `HttpServletRequest` 对象。
   - 创建空的 `HttpServletResponse` 对象用于填充响应数据。
4. **调用 Servlet 的 service() 方法**
   - 容器根据请求方法调用对应的 `doGet()` 或 `doPost()` 方法。
   - 若未重写这些方法，默认返回 `HTTP 405` 错误（方法不支持）。
5. **执行业务逻辑**
   - 在 `doGet()` 或 `doPost()` 中编写业务代码。
   - 通过例如 `response.getWriter().write("Hello World")` 的语句输出响应内容。
6. **返回响应**
   - 容器将 `HttpServletResponse` 中的内容转换为 HTTP 响应报文。
   - 客户端浏览器渲染响应结果。

简单示例如下：

```java
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.println("<h1>Hello, World!</h1>");
    }
}
```

### 简单实操

#### HelloWorld

1. 创建一个项目，然后在project structure中把Tomcat添加为依赖。![image-20250403160626030](https://cdn.jsdelivr.net/gh/Planetesimals-2005/BlogImg/img/image-20250403160626030.png)

2. 创建类之后，继承HttpServlet类，重写service方法

   ```java
   public class UserServlet extends HttpServlet {
   
       @Override
       protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          //其他代码
       }
   }
   ```

​    3.在service方法中定义业务处理代码。

```java
@Override
protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    //1. 从request对象中获取请求中的任何信息（获取请求参数）
    //根据参数名字获取参数值，不论是get请求还是post请求。
    String username = request.getParameter("username");

    //2. 处理请求（业务逻辑）
    String info = "";
    if("Planetesimals".equals(username)){
        info = "Welcome, " + username;
    }
	
    //3. 响应结果（将结果放入response响应给浏览器）
    //PrintWriter 返回的是一个向响应体中写入字符数据的打印流。
    PrintWriter writer = response.getWriter();
    writer.write(info);


}
```

4. 在web.xml中，配置servlet对应的请求映射路径。

   ![image-20250403163957786](https://cdn.jsdelivr.net/gh/Planetesimals-2005/BlogImg/img/image-20250403163957786.png)

5. 在run-Run/Debug Configurations 中确认配置

   ![image-20250403165623727](https://cdn.jsdelivr.net/gh/Planetesimals-2005/BlogImg/img/image-20250403165623727.png)

6. 通过debug模式运行程序，输入username之后，发送请求，成功返回预设info

   ![image-20250403170456511](https://cdn.jsdelivr.net/gh/Planetesimals-2005/BlogImg/img/image-20250403170456511.png)

   ![image-20250403170424895](https://cdn.jsdelivr.net/gh/Planetesimals-2005/BlogImg/img/image-20250403170424895.png)

#### servlet-api.jar包导入

* servlet-api.jar在编码时被需要，在运行时，服务器的环境中由服务软件（Tomcat）提供。

#### Content-type

`	Content-Type` 是 HTTP 协议中的一个头部字段，用于**指明传输数据的媒体类型（MIME 类型）和字符编码**，确保发送方和接收方能正确解析和处理数据。

**语法**：`Content-Type: media-type; charset=character-set`

##### 常见MIME类型

| 类型 | 描述 |
|------|------|
| `text/html` | HTML文档 |
| `text/plain` | 纯文本 |
| `text/css` | CSS样式表 |
| `text/javascript` | JS代码 |
| `image/jpeg` | JPEG图像 |
| `image/png` | PNG图像 |
| `image/svg+xml` | SVG矢量图 |
| `application/json` | JSON数据 |
| `application/xml` | XML数据 |
| `application/pdf` | PDF文档 |
| `application/octet-stream` | 二进制流 |
|`multipart/form-data`| 文件上传 |
|`application/x-www-form-urlencoded`|表单数据|
##### 主要作用

1. **定义数据格式**
   告诉客户端或服务器传输的数据类型，使其能选择正确的解析方式。

2. **指定字符编码**
   附加 `charset` 参数，确保文本内容正确解码，避免乱码。

3. **指导数据解析**

* **请求中**（ POST/PUT），服务器根据content-type解析请求体。

- **响应中**，浏览器根据 `Content-Type` 决定如何渲染内容（显示图片或解析 HTML等）。

4. **Content-type 设置方式**

```java
// 方式1：合并设置
response.setContentType("text/html;charset=UTF-8");

// 方式2：分开设置
//不知道怎么写type可以去web.xml中搜索
response.setContentType("text/html");
response.setCharacterEncoding("UTF-8");