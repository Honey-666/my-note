# Nginx

### nginx入门

#### nginx简介

Nginx ("engine x") 是一个高性能的HTTP 和反向代理服务器,特点是占有内存少，并
发能力强，擅长处理静态资源和高并发,有报告表明能支持高达50,000 个并发连接数,
而tomcat 是web 服务器，擅长处理动态资源。

#### nginx优势

1. 高并发
2. 热部署
3. 快
4. 低功耗
5. 可以反向代理，负载均衡，擅长处理静态资源（css、html、js、img等）

> nginx是C语言开发的，建议在linux上运行

#### nginx下的三个目录

- conf    配置文件目录
- html   页面资源目录
- sbin    执行脚本目录 （运行，重启在这个目录下）

#### nginx常见命令

**回顾前面命令：**

- 查看开放的端口号
  - firewall-cmd --list-all
- 设置开放的端口号
  - firewall-cmd --add-port=80/tcp --permanent
- 重启防火墙
  - firewall-cmd –-reload
- **启动nginx**(在/usr/local/nginx/sbin/目录下)
  - ./nginx
- **停止nginx**（也是在那个目录下）
  - ./nginx -s stop
  - 此方式相当于先查出nginx 进程id 再使用kill 命令强制杀掉进程。
  - ./nginx -s quit
  - 此方式停止步骤是待nginx 进程处理任务完毕进行停止。
- **重启nginx**
  - 方式一先停止再启动：
    - ./nginx -s quit
    - ./nginx
  - 方式二重新加载配置文件：
    - ./nginx -s reload

#### nginx.conf配置文件

**第一部分：全局块**

- 配置nginx的工作进程（work的数量）

- 配置用户及用户组信息 一般默认就行

- nginx运行的一些错误日志
- pid    nginx的进程id

**第二部分：events 块**

* 配置每一个work处理的连接数

**第三部分：http 块（重要）**

- http全局块
  - 请求日志

- **server块（重要）**
  - 每一个server都对应一个虚拟主机，单独对外提供服务
  - root  指定的是nginx安装目录下的某个文件夹的名字
  - server_name  设置监听的域名

### nginx虚拟主机

Nginx 的虚拟主机是指Nginx 内部可以提供类似于主机的服务，自身对外提供服务。

- nginx的虚拟主机有两种
  - 基于端口号的，也就是通过端口号访问
  - 基于域名的，可以通过域名进行访问

> **一个ip地址可以对应多个域名，一个域名只能对应一个ip**

### nginx反向代理

- 正向代理：本质代理的是客户端（如借助vpn访问油管）

- 反向代理：本质代理的是服务器，返回数据给客户端

- 配置反向代理，在nginx.conf配置文件中的server下的location中配置：proxy_pass  http://tomcat的ip+端口号;

例：

```conf
 server {
        listen       80;
        server_name  www.bank.com;

        location / {
            proxy_pass http://127.0.0.1:8080;
            index  index.html index.htm; #去上面的那个目录下找这个页面
        } 
	

        error_page   500 502 503 504  /50x.html; #去上面目录下找错误页面
        location = /50x.html { 
            root   html;
        }

    }
```

- location说明：
  - 语法location 可以有一下几种【=，~，~*，^~，@】
  - =表示精确匹配
  - /a/b模糊匹配，所有以/a/b开头的都能匹配
  - ~用于表示uri 包含正则表达式，并且区分大小写
  - ~*：用于表示uri 包含正则表达式，并且不区分大小写
  - ^~：用于不含正则表达式的uri 前，要求Nginx 服务器找到标识uri 和请求字符
    串匹配度最高的location 后，立即使用此location 处理请求，而不再使用location
    块中的正则uri 和请求字符串做匹配
  - / 和swich case中的deful相同
- location的优先级

```txt
location = /uri =开头表示精确匹配，只有完全匹配上才能生效,匹配成功后立即停止其他匹配。
location ^~ /uri ^~ 开头对URL路径进行前缀匹配，并且在正则之前,匹配成功后立即停止其他匹配。
location ~ pattern ~开头表示区分大小写的正则匹配。
location ~* pattern ~*开头表示不区分大小写的正则匹配。
location /uri 不带任何修饰符也表示前缀匹配,但在正则匹配之后,而且nginx会根据配置的长短来
进行匹配,记忆最长匹配，但不会停止匹配。
location / 通用匹配，任何未匹配到其它location的请求都会匹配到，相当于switch中的default。
注意：上述只有正则表达式的匹配和配置顺序有关，普通字符串和配置顺序无关。
```

**先找精确匹配，如果有立即执行，如果没有往下找普通匹配，找到后不会立即执行，会继续往下找，找到最长的路径进行执行，如果普通匹配也没有，就找正则的匹配，只要找到就执行，不会再往下寻找**

### nginx负载均衡

指的是nginx扛高并发

配置负载均衡：

1. 找到nginx.conf文件，在server上方配置upstream  自定义用户名{

   server ip+端口号（tomcat）

   server ip+端口号（tomcat）

}     然后在location的porxy_pass 后的http://配置自定义名             修改后重新加载配置文件

​	2 启动tomcat

​	3 测试

例：

```conf
upstream myserver {
    server 192.168.92.128:8080;
    server 192.168.92.128:8081;
}
server {
	listen 80;
	server_name www.123.com;
location / {
	proxy_pass http://myserver;

}
	error_page 500 502 503 504 /50x.html;
location = /50x.html {
	root html;
	}
}
```

#### 负载均衡的策略

- 默认采用轮询的方式，就是一人一次
- 配置权重，让性能高的多处理一点
  - 在upstream中的 端口号后面用空格隔开 配置weight=数字

例：

```conf
upstream myserver {
    server 192.168.92.128:8080 weight = 1; #表示有三次请求有一次会走这个
    server 192.168.92.128:8081 weight = 2; #表示有三次请求有二次会走这个
}
server {
	listen 80;
	server_name www.123.com;
location / {
	proxy_pass http://myserver;

}
	error_page 500 502 503 504 /50x.html;
location = /50x.html {
	root html;
	}
}
```

- ip_hassh的方式
  - 同一个ip的客户端发送请求，永远用第一次请求的tomcat处理
  - 在server ip +端口号上配置ip_hash即可

例：

```conf
upstream myserver {
	ip_hash;
    server 192.168.92.128:8080; 
    server 192.168.92.128:8081; 
}
server {
	listen 80;
	server_name www.123.com;
location / {
	proxy_pass http://myserver;

}
	error_page 500 502 503 504 /50x.html;
location = /50x.html {
	root html;
	}
}
```

- ​	fair（第三方）
  - 会找处理速度快的tomcat优先处理

例：

```conf
upstream myserver {
    server 192.168.92.128:8080; 
    server 192.168.92.128:8081; 
    fair
}
server {
	listen 80;
	server_name www.123.com;
location / {
	proxy_pass http://myserver;

}
	error_page 500 502 503 504 /50x.html;
location = /50x.html {
	root html;
	}
}
```

- ​	down表示暂时不参与负载均衡
  - 在端口号后配置 down即可

例：

```conf
upstream myserver {
    server 192.168.92.128:8080 down; 
    server 192.168.92.128:8081; 
    fair
}
server {
	listen 80;
	server_name www.123.com;
location / {
	proxy_pass http://myserver;

}
	error_page 500 502 503 504 /50x.html;
location = /50x.html {
	root html;
	}
}
```

- ​	Backup 表示所有的tomcat都在处理请求或down的时候才会使用 backup这台机器

```conf
upstream myserver {
    server 192.168.92.128:8080 down; 
    server 192.168.92.128:8081 backup; 
    fair
}
server {
	listen 80;
	server_name www.123.com;
location / {
	proxy_pass http://myserver;

}
	error_page 500 502 503 504 /50x.html;
location = /50x.html {
	root html;
	}
}
```

### nginx动静分离

让nginx处理静态资源，tomcat处理动态资源

在配置文件配置location 静态资源存放目录   重新加载文件

例：

```conf
location / {
    proxy_pass http://myserver;
    index index.html index.htm;
}

location ~ \.(gif|jpg|jpeg|png|bmp|swf|js|html|css)$ {
    root /usr/share/nginx/html;
}
```

### nginx集群

部署多台nginx，使用keepalived 虚拟ip让多个nginx在同一个虚拟ip下，当一个nginx发生故障时，另一个nginx能用顶上

### nginx原理及优化配置

一个master有多个work，这些work来争抢，谁争抢到谁处理

一般work和服务器的cpu个数相等最为适宜



请求一次静态资源消耗两次链接

请求一次动态资源消耗四次链接

![image-20210106115400832](typora-user-images\image-20210106115400832.png)