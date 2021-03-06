# 读书笔记 —— 《HTTP 权威指南》

## 一、前言

​	有的人会将自己的认知或经历写成一篇文章甚至一本书，而我，却因为一次经历去阅读别人的作品。记得在很久以前面试前端职位的时候给人问个一个问题：什么是 HTTP 协议 。而当时年时无知的我只能简单的答上一句：超文本传输协议。后来回想，面对这样的问题或许我应该能回答的更好，其实很多知识在脑海里面是存在的，但是却是零散的，所以，也是时候将它们组织起来的，这就是我阅读本书的缘由。希望通过这次阅读，以后谁再问这样的问题我就能给你扯上几个小时。

---

## 二、HTTP 概述

### 1. 什么是 HTTP 

​	`HTTP` 是 `Hypertext Transfer Protocol` 的缩写，翻译为超文本传输协议，是在万维网上进行通信时所使用的协议方案。`HTTP` 有很多应用，但是最著名的是用于 `Web` 浏览器和 `Web` 服务器之间的通信。然而，`Web` 资源的是存储在 `Web` 服务器上的，`Web` 服务器所使用的是 `HTTP` 协议，因此，这些服务器也被称为 `HTTP` 服务器。

### 2. 资源

​	在前面提到了一个词 `Web` 资源，有必要解析一下：最简单的 `Web` 资源就是服务器中的静态文件，比如，文本文件、HTML文件（网页）、JPG图片等等，但是不仅仅包括这些，还包括动态的资源，比如，程序。总之，所有类型的内容来源都是资源。

### 3. MIME类型

​	从前面知道，因特网上资源的种类是很多的，`HTTP` 服务器会为所有的 `HTTP` 对象数据（资源）附加上一个 `MIME` 类型，这个类型是用于描述资源的具体类型的，浏览器也会根据这个类型对不同的资源做出不同的处理。

​	**`MIME` 类型是一种文本标记，表示一种主要的对象类型和一个特定的子类型，中间由一条斜杠来分隔。** 举个例子：

打开谷歌官网，然后通过开发者工具找到这个网页的 `HTML` 文件，找到响应头信息：

![](images/1554287303749.png)

`text/html` 就是一个 `MIME` 类型。 

### 4. URI 与 URL

​	每个 `Web` 服务器资源除了有一个 `MIME` 类型以外，还有一个 “地址”，这样客户端就可以说明它们想要哪个资源了。然而这个地址就是 `URI (Uniform Resource Identifier)` ，意为统一资源标识符，它就像生活中的地址一样，通过一个具体的地址我们就可以找到一个具体的方法了。 `URI` 也正是如此，在世界范围内唯一标识并且定位信息资源。`URI` 有两种形式，分别称为 `URL` 和 `URN` 。

​	`URL` ，统一资源定位符，是资源标识符最常见的形式。`URL` 描述了一台特定服务器上某资源的特定位置。大部分 `URL` 都遵循一种标准格式：

- 方案，说明了访问资源所使用的协议类型。（常见的就是 `HTTP` 协议 ）
- 服务器的因特网地址
- 其他部分指的是这个 `Web` 服务器上的某个资源

​	现实中几乎所有的 `URI` 都是 `URL`，所以这里就不介绍 `URN` 了。当然，`URL` 也不止这点东西，在此只是简单的介绍，后面的章节再来详细讨论。

### 5. 事务

​	一个 `HTTP` 事务由一条请求命令（由客户端发往服务器）和一个响应结果（从服务器发回客户端）组成的。这种通信时通过 `HTTP` 报文的格式化数据块进行的。

#### i. 方法

`HTTP` 支持几种不同的请求命令，这些命令被称为 `HTTP` 方法，每条 `HTTP` 请求报文都包含一个方法，这个方法的作用就是告诉服务器要干嘛，是拿一个资源呢，还是删除一个资源或者其他操作呢。常见的 `HTTP` 方法有：GET、POST、PUT、DELETE 分别表示查询，增加，修改和删除（REST）。

#### ii. 状态码

每条 `HTTP` 响应报文返回时都会携带一个状态码，以告诉客户端请求的处理状况。

通常 2开头的表示成功；3开头的表示重定向；4开头的代表客户端出错；5开头的代表服务器端出错。（在后面再详细介绍）

### 6. 报文

`HTTP` 报文是由一行一行的简单字符串组成的，`HTTP` 报文包括起始行、首部字段和主体。详细的内容在后面章节介绍。



## 三、URL

---

### 1. URL 语法

`URL` 提供了一种定位因特网上任意资源的手段，但这些资源是可以通过各种不同的方案（方案的概念在前面已经提到过，比如 `HTTP` 、`FTP` 等）来访问的，因此 `URL` 语法会随方案不同而有所不同。这是不是意味着每种不同的 `URL` 方案都会有完全不同的语法呢？其实不是的。大部分 `URL` 都遵循通用的 `URL` 语法，而且不同 `URL` 方案的风格和语法都有不少重叠。大多数 `URL` 语法都建立在这个由 9 个部分组成的通用格式上：

**`<scheme>://<user>:<password>@<host>:<port>/<path>;<params>?<query>#<frag>`** 但是不一定都包含这些组件，最重要的还是在前面提到过的三部分 方案（`scheme`）、主机（`host`）和路径（`path`）。但是抱着学习的心态还是来详细的看看所有的组件：

1. 方案 （`scheme`），表示访问服务器时使用哪种协议。
2. 用户（`user`），某些方案访问资源时需要用户名，默认值为匿名。
3. 密码（`password`），用户名后面可能要包含密码，中间由 ：分隔。
4. 主机（`host`），资源宿主服务器的主机名或 IP 地址。
5. 端口（`port`），资源宿主服务器正在监听的端口号，很多方案都有默认端口号，`HTTP` 的默认端口号是 80。
6. 路径（`path`），服务器上资源的本地名，由一个 / 将其与前面的 `URL` 组件分隔开。路径组件的语法是与服务器和方案有关的。
7. 参数（`params`），某些方案会用这个组件来指定输入参数，使用 ；相隔。
8. 查询（`query`），某些方案会用这个组件传递参数，比如 `HTTP` 协议中的 `GET` 请求。
9. 片段（`frag`），一小片或一部分资源的名字，这个字段不会传送给服务器，而是在客户端内部使用的，通过 # 将其与其他部分分隔。

### 2.方案

方案是规定如何访问指定资源的主要标识符，它会告诉负责解析 `URL` 的应用程序使用什么协议。方案组件必须以一个字母开头，由 ：结束，忽略大小写。

### 3.用户名和密码

在因特网上并不是所有的资源都是公开的，有些资源是私有的，只有资源的管理者才能访问，所有，用户名和密码也就有了意义。有些服务器要求输入用户名和密码才允许用户访问数据，`FTP` 服务器就是这样的一个实例。

### 4. 主机与端口

要想在因特网上找到资源，应用程序要知道哪台服务器装载了资源以及在这台服务器上的什么地方可以找到这个资源。然而，`URL` 的主机和端口组件提供了这两组信息。

主机标识了因特网上能够访问资源的宿主服务器，可以使用主机名或 IP 地址来表示主机名。

端口标识了服务器正在监听的网络端口。

### 5. 路径

`URL` 的路径组件说明了资源位于服务器的什么地方，通常很像一个分级的文件系统路径。

### 6. 参数

对于很多方案来说，只有简单的主机名和到达对象的路径是不够的，除了服务器正在监听的端口，以及是否能够通过用户名和密码访问资源外，很多协议都还需要更多的信息才能工作。负责解析 `URL` 的应用程序需要这些协议参数来访问资源。否则，另一端的服务器可能就不会为请求提供服务。为了向应用程序提供它们所需要的输入参数，以便正确地与服务器进行通信， 参数组件就起到作用了。

参数这个组件其实就是一个名值对列表，由 ； 将其他部分分隔。比如： `ftp://prep.ai.mit.edu/pub/gnu;type=d` 就有一个参数 `type` ，值为 d

### 7. 查询字符串

这个组件就比较常见了，在 `HTTP` 协议中，通过 ？以及后面的内容表示一个查询字符串，作用就是发送 `GET` 请求并且将查询字符串中的参数作为请求参数带到服务器中。语法：? key=value&key=value ...

### 8. 片段

这个组件其实也是比较常见的，通常使用在一些篇幅比较长的页面，比如说某个框架的 API 文档，那内容肯定是非常详细的，通过会有一些分章节或者分类的导航链接，然而我们点击这些导航链接就会跳到页面的特定位置，这就是资源的一个片段。这是通过 `HTML` 中的 a 元素的锚点特性实现的。



## 四、HTTP 报文

---

如果说 `HTTP` 是因特网的信使，那么 `HTTP` 报文就是它用来搬东西的包裹了。专业一点描述 `HTTP` 报文就是在 `HTTP` 应用程序之间发送的数据块。这些数据块以一些文本形式的元信息开头，这些信息描述了报文的内容以及含义，后面跟着可选的数据部分。

### 1. 报文流

`HTTP` 使用术语 流入 和 流出 来描述事务的方向。报文流入源端服务器，工作完成以后，会流回用户的 Agent 代理中。不管是请求报文还是响应报文，都会向下游流动。所有报文的发送者都在接收者的上游。

### 2. 报文的语法

`HTTP` 报文可以分为两类：**请求报文和响应报文**。**请求报文会向服务器请求一个动作，而响应报文会将请求的结果返回给客户端。请求报文和响应报文的结构基本相同。**

- 请求报文的格式：

  方法名 请求URL 协议版本  （起始行）

  请求头  （首部）

  （空行）

  请求体（可选）



- 响应报文的格式：

  版本 状态码 原因短语  （起始行）

  响应头  （首部）

  （空行）

  响应体  （主体）

### 3. 报文的组成部分

`HTTP` 报文是简单的格式化数据块。它们由三部分组成：**起始行（对报文进行描述）、首部（包含一些属性）、主体（可选的，包含一些数据）**。

#### i. 起始行

**所有的 `HTTP` 报文都会以一个起始行作为开始，请求报文的起始行说明了要做什么，响应报文的起始行说明发生了什么。**

对于请求报文而言，起始行又称为请求行，包含了一个方法、一个请求URL 和 协议的版本号 。方法描述了服务器应该执行什么操作；请求URL描述了要对哪个资源执行这个方法；版本号用于告知服务器使用哪个版本的协议。

对于响应报文而言，起始行又称为响应行，包含了使用的 `HTTP` 版本号、状态码 和 原因短语（状态码的文本描述）。

#### ii. 首部

`HTTP` 首部的作用是向请求和响应报文中条件了一些附加信息，从本质上说，它们只是一些 K：V 。首部和方法共同决定了客服端和服务器能做什么事情。首部可分为以下的几种：

- 通用首部

  请求报文和响应报文都可以使用的首部。

  - `Date` ，提供日期和时间的标志，说明报文是什么时间创建的。
  - `Connection` ，允许客户端和服务器指定与请求/响应连接有关的选项。
  - `MIME-Version` ，给出了发送端使用的 `MIME` 版本。
  - `Cache-Control` ，用于随报文传送缓存指令。

- 请求首部

  用于请求报文。

  - `Host` ，给出了接收请求的服务器的主机名和端口号。
  - `Accept` ，告诉服务器能够发送哪些媒体类型。
  - `User-Agent` ，将发起请求的应用程序名称告知服务器。
  - `Referer` ，提供了包含当前请求 URI 的文档的 URL。
  - `From` ，提供了客户端用户的 `E-mail` 地址。

- 响应首部

  用于响应报文。

  - `Accept-Ranges` ，对此资源来说，服务器可接受的范围类型。
  - `Server` ，服务器应用程序软件的名称和版本。
  - `Set-Cookie` ，在客户端设置一个令牌，以便服务器对客户端进行标识。

- 实体首部

  提供了有关实体以及内容的信息。

  - `Content-Encoding` ，对主体执行的编码方式。
  - `Allow` ，列出了可以对此主体执行的请求方法。

#### iii. 主体

主体是可选的，它是报文要传输的内容，可以承载很多类型的数据：图片、视频、HTML 文档、应用程序等。



## 五、连接管理

---

### 1. TCP 连接

`HTTP` 通信都是由 `TCP/IP` 承载的，`TCP/IP` 是全球计算机及网络设备都在使用的一种常用的分组交换网络分层协议集。客户端应用程序可以打开一条 `TCP/IP` 连接，连接到可能运行在世界任何地方的服务器应用程序。一旦连接建立起来了，在客户端和服务器的计算机之间交换的报文就不会丢失、受损或失序。

`TCP` 为 `HTTP` 提供了一条可靠的比特传输管道。从 `TCP` 连接一端填入的字节会从另一端以原有的顺序、正确的传送出来。`TCP` 的数据是通过名为 `IP` 分组（或IP数据块）的小数据块来发送的。`HTTP` 要传送一条报文时，会以流的形式将报文数据的内容通过一条打开的 `TCP` 连接按序传输。`TCP` 收到数据流以后，会将数据流砍成被称为作段的小数据块，并将段封装在 `IP` 分组中，通过因特网进行传输，所有这些工作都是由 `TCP/IP` 软件来处理的。每个 `TCP` 段都是由 `IP` 分组承载的，从一个 `IP` 地址发送到另外一个 `IP` 地址。每个 `IP` 分组中都包括：

- 一个 `IP` 分组首部（通常为20字节）

- 一个 `TCP` 段首部（通常为20字节）
- 一个 `TCP` 数据块（0或多个字节）

`IP` 首部包含了源和目的 `IP` 地址、长度和其他一些标记。`TCP` 段的首部包含了 `TCP` 端口号、`TCP` 控制标记，以及用于数据排序和完整性检查的一些数值。

`TCP` 连接是通过 4 个值来识别的：源IP地址、源端口号、目的IP地址、目的端口号。通过这 4 个值就能确定唯一的一条连接。

### 2.TCP 的性能

`HTTP` 紧挨着 `TCP` ，位于其上层，所有 `HTTP` 事务的性能在很大程度上取决于底层 `TCP` 通道的性能。

#### i. HTTP 事务的时延

1. 客户端首先需要根据 `URI` 确定 `Web` 服务器的 `IP` 地址和端口号。如果最近没有对 `URI` 中的主机进行访问，通过 `DNS` 解析系统将 `URI` 中的主机名转换成一个 `IP` 地址可能要花上数秒。(但是大多数客户端独有 `DNS` 缓存)
2. 客户端会向服务器发送一条 `TCP` 连接请求，并等待服务器回送一个请求接受应答。每条新的 `TCP` 连接都会有连接建立时延。
3. 一旦连接建立起来，客户端就会通过新建立的 `TCP` 管道来发送 `HTTP` 请求。数据到达时，`Web` 服务器会从 `TCP` 连接中读取请求报文，并对请求进行处理。
4. 最后，服务器会返回响应，这也需要花费时间。

#### ii. TCP 连接的握手时延

1. 请求新的 `TCP` 连接时，客户端要向服务器发送一个小的 `TCP` 分组（通常是 40~60字节）。这个分组中设置了一个特殊的 `SYN` 标记，说明这是一个链接请求。
2. 如果服务器接受了连接，就会对一些连接参数进行计算，并向客户端回送一个 `TCP` 分组，这个分组中的 `SYN` 和 `ACK` 标记都被置位，说明连接请求已被接受。
3. 最后，客户端向服务器回送一条确认信息，通知它连接已建立。



## 六、Web 服务器

---

### 1. Web 服务器的实现

术语 `Web服务器` 既可以指服务器的软件，也可以指提供 `Web` 页面特定设备或计算机。`Web服务器` 实现了 `HTTP` 协议、管理着 `Web` 资源，并负责提供 `Web` 服务器的管理功能。服务器逻辑和操作共同负责管理 `TCP` 连接。底层操作系统负责管理底层计算机系统的硬件细节，并提供了 `TCP/IP` 网络支持，负责装载 `Web` 资源的文件系统以及控制当前计算活动的进程管理功能。服务器有以下几种实现：

- 可以在标准的计算机系统上安装并运行通用的软件 `Web服务器` 。比如说，`Apache` 大名鼎鼎的 `Tomcat` 服务器，我们只需要在我们的计算机上安装了这款服务器软件，我们的计算机就可以看作一台服务器了。
- 如果不想安装软件，那么可以直接买一台服务器，通常里面的软件会预装并且配置好。比如，BAT 等大厂都有售卖的服务器。
- 嵌入式服务器，嵌入到消费类的产品中的小型 `Web` 服务器。

### 2. Web 服务器的工作

以上说了那么多似乎还没有真正的提到过 `Web服务器` 具体是干嘛的，在我还没有看这本书之前，我是这么认为的：`Web服务器` 就是用来处理客户端的请求，然后返回响应的。额，似乎就是这么回事，但是，书上的描述就比较详细了（非常重要，这或许是你虐待面试官的利器）：

1. 建立连接 —— 接受一个客户端连接，或者如果不希望这个客户端建立连接，就将其关闭。
2. 接收请求 —— 从网络中读取一条 `HTTP` 报文。
3. 处理请求 —— 对请求报文进行解释，并采取行动。
4. 访问资源 —— 访问报文中指定的资源。
5. 构建响应 —— 创建带有正确首部的 `HTTP` 响应报文。
6. 发送响应 —— 将响应回送给客户端。
7. 记录事务处理过程 —— 将与已完成事务有关的内容记录在日志文件中。

#### i. 建立连接

如果客户端已经打开了一条到服务器的持久连接，可以使用那条连接来发送它的请求。否则，客户端需要打开一条新的到服务器的链接。客户端请求一条到 `Web` 服务器的 `TCP` 连接时，服务器会建立连接，判断连接的另一端是哪个客户端，从 `TCP` 连接中将 `IP` 地址解析出来。一旦新的链接并接受，服务器就会将连接添加到其现存的 `Web` 服务器连接列表中，并且监视连接上数据传输。服务器可以随意拒绝或立即关闭任一条连接，有些服务器会因为客户端 `IP` 地址或主机名是未认证的，或者因为它是已知的恶意客户端而关闭连接。当然，服务器也可以使用其他识别技术：

- 客户端主机名识别

  可以利用“反向 `DNS` ” ，对大部分服务器进行配置，以便将客户端 `IP` 地址转换成客户端主机名。`Web服务器` 可以将客户端主机名用于详细的访问控制和日志记录。但是这种方式是很浪费时间的，很多大容量的服务器要么会禁止主机名解析，要么只允许对特定内容进行解析。

- 通过 `ident` 识别客户端 （了解）

  服务器可以通过 `ident` 协议（前提是服务器得支持这个协议）找到 `HTTP` 连接的用户名。如果客户端支持 `ident` 协议，就在 `TCP` 端口113 上监听 `ident` 请求。

#### ii. 接收请求

连接上有数据到达时，服务器会从网络连接中读取数据，并将请求报文中的内容解析出来。以下就是具体的解析步骤：

- 解析请求行，查找请求方法、指定的资源标识符（URI）以及版本号
- 读取报文首部
- 读取请求主体（非必须）

#### iii. 处理请求

一旦服务器接收到请求，就可以根据方法、资源、首部以及可选的主体来进行处理，由于不同的请求方法具有不同的处理方式，在此就先不做详细说明。

#### iv. 访问资源

在前面也提到过，可以认为在服务器上存储的一切事物都是资源，所以也可以说服务器就是资源服务器，它们负责发送预先创建好的内容，比如，`HTML` 页面。

- `docroot` ，这是最简单的资源映射形式，就是用请求 `URI` 作为名字来访问服务器中的文件。
- 目录列表，服务器可以接受对目录 `URL` 的请求，其路径可以解析为一个目录，而不是具体的资源。在这种情况下，大多数的服务器都会去寻找一个名为 `index.html` 或 `index.htm` 的文件来代替此目录。
- 动态资源映射，其实就是一个复杂的后台应用程序在控制管理着复杂的资源关系，动态的返回响应。

#### v. 构建响应

- 响应实体。如果事务处理产生了响应主体，就将内容放在响应报文中回送过去。响应报文通常包含：
  1. 描述了响应主体 `MIME` 类型的 `Content-Type` 首部
  2. 描述了响应主体长度的 `Content-Length` 首部
  3. 实际报文的主体内容

- 重定向。服务器有时会返回重定向响应而不是成功的报文。服务器可以将浏览器重定向到其他地方执行请求。

#### vi. 发送响应

服务器将构建好的响应（可能是响应实体，也可能是一个重定向）返回到客户端中。对于非持久连接而言，服务器在发送完成整个报文以后，关闭自己一端的链接。

#### vii. 记录日志

当一条 `HTTP` 事务结束时，服务器会在日志文件中添加一个记录来描述已执行的事务。详细的在后面章节介绍。



## 七、代理

---

代理，指的是代理服务器，是网络的中间实体。代理位于客户端和服务器之间，扮演“中间人”的角色，在各端点之间来回传送 `HTTP` 报文。代理服务器可以是某个客户端专用的（称为私有代理），也可以是多个客户端共享的（公共代理）。那么，代理有什么作用呢？我们为什么要使用代理呢？使用代理不是更麻烦吗，直接让客户端跟服务器之间进行通信就好了，为什么中间还要夹上代理呢？

### 1. 代理的作用

- 安全防火墙

  网络安全工程师通常会使用代理服务器来提高安全性。代理服务器会在网络中的单一安全节点上限制那些应用层协议的数据可以流入或流出一个组织。还可以提供用来消除病毒的 `Web` 和 `E-Mail` 代理使用的那种挂钩程序。

- `Web` 缓存

  代理缓存维护了常用文档的本地副本，并将它们按需提供，以减少缓慢且昂贵的网络通信。

- 反向代理

  代理可以假扮 `Web` 服务器，这些被称为替代物或反向代理的代理接受发送给服务器的真实请求，但是，如真是服务器不同的是，它们可以发起与其他服务器的通信，以便按需定位请求的内容。

- 内容路由器

  代理服务器可以作为内容路由器使用，根据网络流量状况以及内容类型将请求导向特定的 `Web` 服务器。内容服务器也可以用来实现各种服务器级别的请求。比如，如果用户或内容提供者付费要求提供更高的性能，内容路由器可以将请求转发到附近的复制缓存，或者如果用户申请了过滤服务，还可以通过过滤代理来转发请求。

- 转码器

  代理服务器在将内容发送给客户端之前，可以修改内容的主体格式。在这些数据表示法之间进行的透明转换被称为转码。

- 匿名者

  匿名者代理会主动从 `HTTP` 报文中删除身份特性（ IP 地址、cookie或会话等），从而提供私密度。

### 2. 代理的结构

需要知道的是，在客户端与服务器之间可能存在着零到多个代理服务器。也就是说，代理服务器可以是多个的，然而代理服务器之间也就形成了级联的关系。下一个入口代理（靠近服务器）被称为父代理，下一个出口代理（靠近客户端）被称为子代理。这个层次不一定是静态的，代理服务器可以根据众多因素，将报文转发给一个不断变化的代理服务器和原始服务器。比如：如果请求的对象属于一个收费使用内容分发的 `Web` 服务器，代理就会将请求发送给附近的一个缓存服务器，这个服务器会返回已缓存的对象。又比如：请求的是特定类型的图片，访问代理会将请求转发给一个特定的压缩代理，这个代理会去获取图片，然后对其进行压缩。除此以外，还有：

- 负载均衡，子代理可能会根据当前父代理上的工作负载级别来决定如何选择一个父代理，以均衡负载。
- 地址位置附近的路由，说白了就是 “就近原则” ，谁近跑谁那。
- 协议/类型路由，子代理可能会提供 `URI` 将报文转发到不同的父代理或原始服务器上。

### 3. 代理是如何获取流量的

获取流量这个概念可能会让人有点迷糊，说白了就是如何获取存在感，或者说，怎么使用上代理。专业术语上就是， `HTTP` 流量（报文）怎么才能首先流向代理服务器，有以下几种方式：

- 修改客户端

  很多浏览器都支持收工和自动的代理配置。如果将客户端配置为使用代理服务器，客户端就会将 `HTTP` 请求有意地发送给代理，而不是直接到达原始的服务器。

- 修改网络

  网络基础设施可以通过技术手段，在客户端不知情的情况下，拦截网络流量并将其导入代理。这种代理也称为拦截代理。

- 修改 DNS 的命名空间

  放在 `Web` 服务器之前的代理服务器会直接假扮服务器的名字和 IP地址，这样，所有请求就会发送给这个代理，而不是服务器。如果需要这样的实现，可以手工编辑 DNS 名称列表或者用特殊的动态 DNS 服务器根据需要来确定社党的代理。

  