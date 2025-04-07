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
```

### Servlet 注解

#### @webservlet

**作用：**用于声明一个 Servlet 类，并配置其 URL 映射和初始化参数（替代 `web.xml` 中的 `<servlet>` 和 `<servlet-mapping>`）。

 **核心参数**

|           参数           |       类型       | 默认值  |                        说明                        |
| :----------------------: | :--------------: | :-----: | :------------------------------------------------: |
| `urlPatterns` 或 `value` |    `String[]`    |   无    | **必填**，URL 匹配规则（如 `"/user"`, `"/api/*"`） |
|          `name`          |     `String`     |  类名   |               Servlet 名称（可省略）               |
|     `loadOnStartup`      |      `int`       |  `-1`   |          启动顺序（≥0 时容器启动即加载）           |
|       `initParams`       | `WebInitParam[]` |   无    |         初始化参数（`@WebInitParam` 注解）         |
|     `asyncSupported`     |    `boolean`     | `false` |                  是否支持异步处理                  |

```java
@WebServlet(
urlPatterns = "/user",
initParams = {
    @WebInitParam(name = "maxUsers", value = "100")
},
loadOnStartup = 1
    //表示在tomcat启动时就加载这个servlet
    //如果是默认值-1 则在第一次请求的时候加载
)
public class UserServlet extends HttpServlet {
    //下接 GET/POST 方法实现
}
```
#### **@WebFilter**

**作用:**声明过滤器，拦截指定请求（替代 `web.xml` 中的 `<filter>` 和 `<filter-mapping>`）。

**核心参数**

|       参数        |        类型        |  默认值   |                             说明                             |
| :---------------: | :----------------: | :-------: | :----------------------------------------------------------: |
|   `urlPatterns`   |     `String[]`     |    无     |                   按 URL 匹配（如 `"/*"`）                   |
|  `servletNames`   |     `String[]`     |    无     |                     按 Servlet 名称匹配                      |
| `dispatcherTypes` | `DispatcherType[]` | `REQUEST` | 过滤的请求类型（`REQUEST`, `FORWARD`, `INCLUDE`, `ERROR`, `ASYNC`） |

```java
@WebFilter(
urlPatterns = "/*",
dispatcherTypes = { DispatcherType.REQUEST, DispatcherType.FORWARD }
)
public class LogFilter implements Filter {
    @Override
    public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) {
        System.out.println("Request URL: " + ((HttpServletRequest) req).getRequestURI());
        chain.doFilter(req, res); // 放行请求
    }
}
```
#### **@WebListener**

**作用：**声明监听器，监听 Servlet 上下文、会话或请求的生命周期事件（替代 `web.xml` 中的 `<listener>`）。

**使用方式**

- 直接标记在实现了特定接口的类上（如 `ServletContextListener`, `HttpSessionListener`）。
- **无需参数**，由接口类型自动识别监听目标。

```java
@WebListener
public class AppLifecycleListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("应用启动: 加载初始配置");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("应用关闭: 释放资源");
    }
}
```

#### **@MultipartConfig**

 **作用**

标记 Servlet 支持文件上传，需配合 `HttpServletRequest#getPart()` 使用。

 **核心参数**

|        参数         |   类型   | 默认值 |                说明                |
| :-----------------: | :------: | :----: | :--------------------------------: |
|     `location`      | `String` |   空   |       上传文件的临时存储路径       |
|    `maxFileSize`    |  `long`  |  `-1`  |      单个文件最大大小（字节）      |
|  `maxRequestSize`   |  `long`  |  `-1`  |      整个请求最大大小（字节）      |
| `fileSizeThreshold` |  `int`   |  `0`   | 文件大小超过阈值时写入磁盘（字节） |

```java
@WebServlet("/upload")
@MultipartConfig(
    maxFileSize = 1024 * 1024 * 10, // 10MB
    maxRequestSize = 1024 * 1024 * 50 // 50MB
)
public class FileUploadServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse res) {
        Part filePart = req.getPart("file");
        filePart.write("/data/uploads/" + filePart.getSubmittedFileName());
    }
}
```

#### **@ServletSecurity**

**作用**：定义 Servlet 的访问控制规则（如角色、HTTP 方法约束）。

常与 `@HttpConstraint` 和 `@HttpMethodConstraint` 配合使用。

```java
@WebServlet("/admin")
@ServletSecurity(
    @HttpConstraint(rolesAllowed = "ADMIN"),
    httpMethodConstraints = {
        @HttpMethodConstraint(value = "GET", rolesAllowed = "USER")
    }
)
public class AdminServlet extends HttpServlet { 
	//下接具体的实现代码
}
```

### Servlet 生命周期

**生命周期（Lifecycle）** 指一个对象或系统从 **创建、运行到销毁** 的完整过程。在软件中，生命周期管理用于控制资源的分配、使用和释放，确保程序高效稳定运行。

* Servlet 的生命周期由 Servlet 容器（如 Tomcat、Jetty）管理，通常分为四个阶段。

#### **实例化（Instantiation）**

`（一般直接归类在初始化阶段，此处单独调出）`

- **触发时机**：Servlet 容器（如 Tomcat）启动时，或首次收到针对该 Servlet 的请求时。

- 行为：

  - 容器通过 **默认构造函数**（无参构造方法）创建 Servlet 的实例。
  - 每个 Servlet 类在容器中**只有一个实例**（单例模式），所有请求共享此实例。

  - 实例化由容器自动完成，开发者无法直接干预。
  - 如果需要在实例化时执行逻辑，需通过默认构造函数实现（但通常不推荐在此阶段操作资源）。

#### **初始化（Initialization）**

- **触发时机**：实例化完成后立即执行。

- **核心方法**：`init(ServletConfig config)`。

- 行为：

  - 容器调用 `init` 方法，完成 Servlet 的初始化配置（如加载配置文件、建立数据库连接等）。
  - `ServletConfig` 参数提供 Servlet 的配置信息（如 `web.xml` 或注解中的初始化参数）。

  - 开发者可重写 `init` 方法，添加自定义初始化逻辑。
  - 若初始化失败，容器会抛出 `ServletException` 并标记该 Servlet 为不可用。

####  **请求处理（Service）**

- **触发时机**：每次收到客户端请求时。

- **核心方法**：`service(ServletRequest req, ServletResponse res)`。

- 行为：

  - 容器为每个请求创建一个新线程，并调用 `service` 方法。
  - `service` 方法根据 HTTP 方法（GET、POST 等）分发到对应的 `doGet()`、`doPost()` 等方法。

  - 开发者通常直接重写 `doGet()`、`doPost()` 等方法，而非 `service` 方法。

#### **销毁（Destruction）**

- **触发时机**：容器关闭或应用被卸载时。

- **核心方法**：`destroy()`。

- 行为：

  - 容器调用 `destroy` 方法，释放 Servlet 占用的资源（如关闭数据库连接、清理缓存等）。
  - 销毁后，Servlet 实例会被垃圾回收。

  - 开发者可重写 `destroy` 方法，添加自定义清理逻辑。

#### 示例代码

```java

//实例化：容器调用默认构造函数（开发者一般不重写）
public ServiceLifeCycle() {
    System.out.println("构造器被调用");
}

 // 初始化：开发者可重写 init()
@Override
public void init() throws ServletException {
    System.out.println("初始化");
}

// 请求处理：开发者重写 doGet()或是service
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) {
   System.out.println("处理 GET 请求");
}

// 销毁：开发者可重写 destroy()
@Override
public void destroy() {
    System.out.println("销毁");
}
```

#### 横向对比

|     阶段     |       触发时机       |         开发者控制权          |      典型用途      |
| :----------: | :------------------: | :---------------------------: | :----------------: |
|  **实例化**  | 容器启动或首次请求时 |    无（依赖默认构造函数）     |    创建单例实例    |
|  **初始化**  |     实例化完成后     |         重写 `init()`         | 加载资源、配置参数 |
| **请求处理** |       每次请求       | 重写 `doGet()`、`doPost()` 等 |    处理业务逻辑    |
|   **销毁**   |  容器关闭或应用卸载  |       重写 `destroy()`        |      释放资源      |

### Default-Servlet

**核心作用：**

1. **处理静态资源**
   - 当请求路径对应到 Web 应用的静态文件时，Default Servlet 负责读取文件并返回内容。
   - 例如：直接访问 `http://localhost:8080/index.html`，实际由 Default Servlet 处理。
2. **兜底请求处理**
   - 如果请求路径未匹配任何自定义 Servlet 的 URL 模式（`urlPatterns`），则由 Default Servlet 处理。
   - 例如：访问 `http://localhost:8080/unknown-path`，可能返回 404 错误（由 Default Servlet 抛出）。
3. **目录列表展示（可选）**
   - 某些容器允许配置 Default Servlet 显示目录中的文件列表（类似 FTP 目录浏览），但默认通常禁用。



### Servlet 继承结构

#### Servlet接口

- **核心方法:**

  ```java
  void init(ServletConfig config);     // 初始化
  void service(ServletRequest req, ServletResponse res); // 处理请求
  void destroy();                      // 销毁
  ServletConfig getServletConfig();    // 获取配置
  String getServletInfo();             // 返回描述信息
  ```

 - **作用**: 定义 Servlet 的**基本生命周期方法**，所有 Servlet 必须实现此接口。

- ```java
  public interface Servlet {
  //初始化方法，构造完毕后，由tomcat自动调用完成初始化功能的方法
  void init(ServletConfig var1) throws ServletException;
  //获得ServletConfig对象的方法
  ServletConfig getServletConfig();
  //接收用户请求，向用于响应信息的方法
  void service(ServletRequest var1, ServletResponse var2) throws ServletException, IOException;
  //返回Servlet字符串形式描述信息的方法
  String getServletInfo();
  //Servlet在回收前。由tomcat调用的销毁方法，往往用于做资源的释放工作
  void destroy();
  }
  ```

####  **GenericServlet 抽象类**

- **继承关系**: 实现 `Servlet` 接口，并部分实现其方法。
- 核心改进:
  - 提供 `ServletConfig` 的默认实现，简化 `init()` 方法的使用。
  - 添加 `log()` 方法，支持日志记录。
    - **协议无关性**：适用于非 HTTP 协议（如 FTP），但实际极少使用。

*  

  ```java
  public abstract class GenericServlet implements Servlet{
      private transient ServletConfig config;
      @Override
      public void destroy() {
          //将抽象方法重写为普通方法，在方法内部没有任何实现的代码,称之为平庸实现。
      }
      @Override
      public void init(ServletConfig config) throws ServletException {
          //tomcat 在调用init方法时会读取配置信息进入一个ServletConfig对象并将该对象传入init方法
          //将config对象存储为当前属性
          this.config = config;
          //调用重载的无参init方法
          this.init();
      }
      public void init() throws ServletException {
          //无参init方法
      }
      @Override
      public ServletConfig getServletConfig() {
          //返回config对象
          return config;
      }
      public abstract void service(ServletRequest req, ServletResponse res) throws ServletException, IOException;
      //再次抽象声明service方法
  }
  ```

  

#### **HttpServlet类**

- **继承关系**: 继承 `GenericServlet`，专为 HTTP 协议设计。

- 核心方法：

  ```java
  protected void doGet(HttpServletRequest req, HttpServletResponse resp);  // 处理 GET 请求
  protected void doPost(HttpServletRequest req, HttpServletResponse resp); // 处理 POST 请求
  // 其他方法：doPut(), doDelete(), doHead(), 等
  ```


- 关键逻辑:
  - 覆盖 `service()` 方法，根据 HTTP 方法（GET/POST 等）分发到对应的 `doXxx()` 方法。
  - 默认返回 405 错误（若未实现特定 HTTP 方法的处理逻辑）。

#### **关键类的职责对比**

|      类/接口       |                             职责                             |
| :----------------: | :----------------------------------------------------------: |
|    **Servlet**     | 定义生命周期方法（`init`, `service`, `destroy`），与协议无关。 |
| **GenericServlet** | 提供基础实现（如日志、`ServletConfig` 管理），仍与协议无关。 |
|  **HttpServlet**   | 扩展 HTTP 协议支持，封装 `HttpServletRequest` 和 `HttpServletResponse`。 |

#### **为什么通常继承 HttpServlet？**

1. **HTTP 专用优化**：
  - 直接处理 HTTP 请求，无需解析原始协议数据。
   - 提供 `doGet()`, `doPost()` 等便捷方法。
2. **避免重复造轮子**：
  - `HttpServlet` 已实现 HTTP 方法分发逻辑（`service()` 方法），开发者只需关注业务代码。
3. **规范性与兼容性**：
  - 遵循 Servlet 规范，适配所有主流 Web 容器（如 Tomcat、Jetty）。