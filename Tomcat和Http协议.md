# Tomcat

#### 服务器的概述

* 由硬件和软件构成的

#### 常见的javaweb服务器

* tomcat
* jboos
* weblogic

#### Tomcat下的七个文件

* bin目录启动关闭tomcat服务器
* lib         jar包（常用的jar包servlet和jsp）
* conf     配置文件
* logs    日志文件
* temp    临时文件
* webapps    本地代码部署的位置（web应用的部署位置，每个文件夹都是一个项目）
* work   session和jsp的工作区

##### **404错误是资源找不到错误**

##### idea中的Tomcat相当于将本地的Tomcat复制了一份，在idea中的tomcat相当于一份tomcat镜像（这个在idea文件夹的目录中的tomcat中可以看到）





# HTTP

* http是超文本传输协议
* 客户端与服务端的响应称为报文
  * 浏览器向服务器发送称为请求报文
  * 服务器发送给浏览器称为响应报文
* http规定了报文的格式

#### **http1.0是短链接，http1.1是长链接**

* 短链接就是传输一次建立一次链接，每当一次用完后进行关闭等下次传输的时候在创建（浪费资源）
  * 举例五个问题一个一个问不是一次性问完
* 长连接无论由多少链接都统一建立一次链接，等到使用完毕进行关闭

#### 浏览器请求头里面的信息

**GET请求HTTP协议的内容**

1、请求行
(1) 请求的方式GET
(2) 请求的资源路径[+?+请求参数]
(3) 请求的协议的版本号HTTP/1.1
2、请求头
key : value 组成不同的键值对，表示不同的含义。

![image-20201115152433110](typora-user-images\image-20201115152433110.png)

**POST请求HTTP协议的内容**

1、请求行
(1) 请求的方式POST
(2) 请求的资源路径[+?+请求参数]
(3) 请求的协议的版本号HTTP/1.1
2、请求头
1) key : value 不同的请求头，有不同的含义
空行
3、请求体===>>> 就是发送给服务器的数据

![image-20201115153517647](typora-user-images\image-20201115153517647.png)

#### HTTP中的请求报文

* get在network中看不到请求报文因为他保存在地址栏中

* post在network的form data中可以查看传输的报文

* 

  ![image-20201114130537795](typora-user-images\image-20201114130537795.png)

#### HTTP响应报文

1、响应行
(1) 响应的协议和版本号
(2) 响应状态码
(3) 响应状态描述符
2、响应头
(1) key : value 不同的响应头，有其不同含义
空行
3、响应体---->>> 就是回传给客户端的数据

![image-20201115154552250](typora-user-images\image-20201115154552250.png)

#### 响应状态码

* **200：**请求成功，浏览器会把响应体内容（通常是html）显示在浏览器中；
* **404：**请求的资源没有找到，说明客户端错误的请求了不存在的资源；
* **500：**请求资源找到了，但服务器内部出现了错误；
* **302：**重定向，当响应码为302时，表示服务器要求浏览器重新再发一个请求，服务器会发送一个响应头Location，它指定了新请求的URL地址；

### 扩展：哪些是GET 请求，哪些是POST 请求

#### GET 请求有哪些：

1、form 标签method=get
2、a 标签
3、link 标签引入css
4、Script 标签引入js 文件
5、img 标签引入图片
6、iframe 引入html 页面
7、在浏览器地址栏中输入地址后敲回车

#### POST 请求有哪些：

8、form 标签method=post

#### MIME 类型说明：（了解）

* MIME 是HTTP 协议中数据类型。
* MIME 的英文全称是"Multipurpose Internet Mail Extensions" 多功能Internet 邮件扩充服务。MIME 类型的格式是“大类型/小类型”，并与某一种文件的扩展名相对应。

**常见的MIME 类型：**

![image-20201115155806341](typora-user-images\image-20201115155806341.png)

![image-20201115155814285](typora-user-images\image-20201115155814285.png)

