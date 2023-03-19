# Maven

### maven简介

* maven是一个java项目管理工具和自动化构建工具，还可以进行jar包依赖的管理

* 读音：麦文

### maven的作用

* 作用一 java项目工具【POM:peoject object  mode】
* 作用二 自动化构建工具（一键完成这些操作：源码---》编译--》测试---》测试报告---》打包---》部署）
* 作用三 及逆行jar包依赖的管理

自动化构建工具：Ant---->Maven---->Gradle

### Maven的优势

* 一：获取jar包，只需要去固定地址下载即可（去中央仓库下载）
* 二：节省磁盘空间，不需要每个项目都导入jar包了，大家都去中央仓库拿即可
* 三：便于项目按照业务逻辑进行拆分，和便于跨团队协作开发
* 四：解决了jar包之间的依赖（jar包依赖就是导入一个jar包，但是他和另一个jar包还有关联，需要一起倒入两个，那么现在有了maven只需要导入一个就行了，不管它们之间的依赖,另一个会自动导入）
* 便于项目进行分布式开发，便于分模块进行部署

### Maven项目目录结构

约定>配置>编码

Maven下项目的目录结构：

项目名
	src
		main------------------存放业务逻辑相关代码和配置文件
			java--------------存放业务逻辑相关代码
			resources---------存放业务逻辑相关配置文件
		test
			java--------------存放测试代码
			resources---------存放测试代码需要的配置文件
	pom.xml-------------------配置项目坐标、依赖和插件信息

> **扩展：静态导入后会加载这个类的静态属性和方法，不需要类名直接可以调用**

### 常用命令

> **注意：一定要在pom.xml 文件所在的目录运行maven 项目, 查看根目录变化**

* mvn -v或mvn - version 查看当前maven版本(不需要在maven项目目录下)
* mvn clean      清理，将项目根目录下的target目录删除
* mvn compile    编译，将src/main/java 代码编译成class 文件
* mvn  test   测试，将src/test/java 代码编译成class 文件,并执行测试方法
* mvn  package  打包，将当前项目打成Jar、war、pom 包
* mvn  install  安装，将当前项目打成的包安装到本地仓库中

### maven生命周期(了解)

默认有三个生命周期，每个生命周期负责的任务不同

* clean生命周期 ：主要为完成清理工作
* Default生命周期：主要完成项目的编译、测试、测试报告、打包、安装等操作
* site生命周期: 完成项目站点的生成【基本不用,了解】

> **注意：每个生命周期的命令在执行的时候，默认都是从该生命周期第一个命令开始执行。**

### maven依赖

仓库(repository)：存放东西的容器

* maven仓库：存放maven项目的jar包的文件夹
* Maven仓库配置：manven安装目录/conf/settings.xml
* Maven仓库默认位置：C盘下用户下的/.m2/repository

#### maven的仓库划分

* maven的本地仓库：在当前计算机上的存放maven  jar包文件夹
* maven的远程仓库：不在本地计算机存放的maven  jar包文件夹
  * 私服：通常是公司局域网内
  * 中央仓库：在美国
  * 镜像仓库：架设在各大洲

#### maven的坐标与依赖

* **maven的坐标**：用来确定jar包在本地仓库中的位置
  * 格式：
    * groupid标签：一般用公司域名反写 或 公司域名反写+项目名
    * artifactid标签：项目名  或项目名_模块名（根据上面写没有写项目名）
    * version标签：项目版本信息

简称GAV坐标

* **maven的依赖**

```xml
<!--以来标签，用在dependencys标签里，每一对dependency标签是一个依赖 -->
<dependency>
    <!--本地仓库jar包所在的包名-->
  <groupId>junit</groupId>
    <!--本地仓库jar包所在的包名-->
  <artifactId>junit</artifactId>
     <!--jar包的版本-->
  <version>4.12</version>
  </dependency>
```

* Jar 包的目录：groupId+artifactdId+version
* Jar 包的名字: artifactdId-version

> **Maven 坐标主要是用来确定当前项目打成的Jar 包在本地仓库的位置。**
> **Maven 的依赖主要是用来确定当前项目引入的Jar 包在本地仓库的位置。**

### maven项目分类

* jar工程：打成jar包就叫jar工程

* pom 工程：当前项目是父工程、或者聚合工程的时候，当前项目要打成pom 包
* war 工程：当前项目如果要被部署服务器上，就打成war 包。

### 去哪儿找Jar 包依赖

* http://search.maven.org/
* http://mvnrepository.com/【建议使用这个】

### 创建jar工程

在idea中创建project中选中maven，在create from archetype打上对勾然后选中底下的archetype-quickstart，活着不选择之际next默认就是jar工程

### 创建war工程

在idea中创建project中选中maven，在create from archetype打上对勾然后选中底下的archetype-webapp

### 创建pom工程

首先先创建一个jar工程或war工程，然后在pom.xml中的坐标地下添加packing标签为pom，在删除工程中的src

### war工程几个问题

#### tomcat解决控制台乱码问题

1.  点击Help => Edit custom VM Options，在最后面添加
   - -Dfile.encoding=UTF-8
2. 点击右上角Edit Configurations,在tomcat配置中的VM option中添加
   - -Dfile.encoding=UTF-8
   - 在Startup/Connection选项卡的Run和Debug添加一个key为JAVA_TOOL_OPTIONS, value为-Dfile.encoding=UTF-8的环境变量
3.  保存后重启idea，可以发现控制台中文乱码显示正常了

#### JSP 报错问题

在pom.xml添加以下代码

```xml
<!-- servlet-api -->
<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>javax.servlet-api</artifactId>
	<version>3.1.0</version>
	<scope>provided</scope>
</dependency>
<!-- jsp-api -->
<dependency>
	<groupId>javax.servlet.jsp</groupId>
	<artifactId>javax.servlet.jsp-api</artifactId>
	<version>2.2.1</version>
	<scope>provided</scope>
</dependency>
```

#### EL 表达式不起作用

<%@page language="java" pageEncoding="UTF-8" isELIgnored="false" %>

添加 isELIgnored="false"  不忽略EL表达式

#### 修改JDK 版本问题

在pom.xml中添加以下代码：

```xml
<build>
	<plugins>
		<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-compiler-plugin</artifactId>
			<version>3.5.1</version>
			<configuration>
			<source>1.8</source>
			<target>1.8</target>
			<encoding>UTF-8</encoding>
			</configuration>
		</plugin>
	</plugins>
</build>
```

#### Web.xml 文件中约束版本问题

在settings中设置模板

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
version="4.0">
</web-app>
```

![image-20201215183047909](typora-user-images\image-20201215183047909.png)

### Tomcat 插件

Tomcat 插件和本地tomcat 无关，即使没有本地tomcat，tomcat 插件依旧好使。

在pom.xml中添加

```xml
<build>
<plugins>
<plugin>
<groupId>org.apache.tomcat.maven</groupId>
<artifactId>tomcat7-maven-plugin</artifactId>
<version>2.2</version>
<configuration>
<!-- path标签设置项目名-->
<path>/ssm</path>
<!-- port标签设置端口号-->
<port>8888</port>
<!-- 该参数设置GET请求乱码问题-->
<uriEncoding>UTF-8</uriEncoding>
</configuration>
</plugin>
</plugins>
</build>
```

### 依赖的传递性

* 相同类型的jar包，版本不同时:
  * 路径不同时，路径短者优先
  * 路径相同时，先声明者优先(在pom.xml中谁声明在上面谁优先)

* 解决Jar 包冲突的三种解决方案：
  * 第一种：路径不同时，路径短者优先
  * 第二种：路径相同时，先声明者优先
  * 第三种：排除法

```xml
pom.xml中排除法代码
<dependency>
<groupId>com.offcn.maven</groupId>
<artifactId>Hello</artifactId>
<version>0.0.1-SNAPSHOT</version>
<!--排除标签-->
<exclusions>
    <!--每一对标签代表要排除的一个jar包-->
<exclusion>
<groupId>log4j</groupId>
<artifactId>log4j</artifactId>
</exclusion>
</exclusions>
</dependency>
```

### 父工程

父工程：必须打成pom 包,父工程是用来管理子工程Jar 包版本信息的，不做实际依赖。

父工程的作用：主要用于管理子工程的jar包信息，这样在子工程中引用jar包就不需要写版本信息了

在子工程中引用父工程示例代码：

```xml
使用parent标签
<parent>
<!-- 父工程坐标（gav）,直接去父工程复制即可-->
<groupId>...</groupId>
<artifactId>...</artifactId>
<version>...</version>
<relativePath>从当前目录到父项目的pom.xml文件的相对路径</relativePath>
    <!--示例    ../子工程名/pom.xml 
		../是返回上一层目录 
	-->
</parent>
<!--==================================================================================-->
在子项目中重新指定需要的依赖，不需要版本号
<dependencies>
<dependency>
<groupId>junit</groupId>
<artifactId>junit</artifactId>
</dependency>
</dependencies>
```

在父工程中管理依赖示例代码：

将parent 项目中的dependencies 标签，用dependencyManagement 标签括起来

```xml
方式一：
<dependencyManagement>
<dependencies>
<dependency>
<groupId>junit</groupId>
<artifactId>junit</artifactId>
<version>4.9</version>
<scope>test</scope>
</dependency>
</dependencies>
</dependencyManagement>
<!--==================================================================================-->
方式二:
<!-- 在properties标签内部可以设置jar包版本信息-->
<properties>
<!-- 标签名可以随意写【见名知意】,值不是随便写的-->
<junit.version>4.9</junit.version>
</properties>

<dependencyManagement>
<dependencies>
<dependency>
<groupId>junit</groupId>
<artifactId>junit</artifactId>
<!-- 通过${}表达式引用上面properties中定义好的版本号信息-->
<version>${junit.version}</version>
<scope>test</scope>
</dependency>
</dependencies>
</dependencyManagement>
```

### 聚合工程

* 聚合工程：必须打成pom 包,聚合是用来管理其它工程的工程。
* 聚合工程的作用：将其他工程当成一个模块统一进行管理
* 聚合工程在加载的时候会先加载自己管理的子工程

* 创建集合工程：
  1. 创建一个pom工程
  2. 在pom工程中添加modules标签
  3. 在modules标签中添加module标签，每对module代表一个管理的子工程模块

示例代码：

```xml
<!-- 聚合工程是把其它工程当做一个模块管理的-->
<modules>
<module>../app01/pom.xml</module>
<module>../app02/pom.xml</module>
<module>../app03/pom.xml</module>
</modules>
```

> **注意：实际开发中，父工程一般同时也会作为聚合工程一起使用。**

### 在idea 中创建父子同窗的项目的两种方式

* 第一种方式：先创建一个空工程（new project----->>empty project），然后在空工程下面创建两个module，一个父工程、一个子工程，他们在同一个目录里边（平级）
* 第二种方式：直接创建父工程，然后选中父工程，创建子模块即可（在父工程下直接new module）。

### 依赖范围

定义：决定了jar包实在编译期、测试期、运行期有效

```xml
<dependency>
<groupId>junit</groupId>
<artifactId>junit</artifactId>
<version>4.9</version>
<scope>test</scope>
</dependency>

scope标签里的值：
	compile：默认的，表示该jar包在编译期、测试期、运行期都有效
	test：表示该jar包只是在测试期间有效，只有junit的依赖范围为test
	provided：表示该jar包的存在只在编译期、测试期有效，运行期无效
		常见的provided范围jar包servlet-api、jsp-api
```

### maven 项目中找不到sql 映射文件问题，这里有两种解决方案

**方案一**：在pom 文件的build 标签中加入一下代码即可

```xml
<build>
	<resources>
		<resource>
			<directory>src/main/resources</directory>
			<includes>
			<include>**/*.properties</include>
			<include>**/*.xml</include>
			</includes>
			<filtering>false</filtering>
		</resource>
            
	<resource>
		<directory>src/main/java</directory>
		<includes>
		<include>**/*.properties</include>
		<include>**/*.xml</include>
		</includes>
		<filtering>false</filtering>
	</resource>
</resources> 
</build>
```

**方案二**：在src/main/resources 目录下创建与src/main/java 目录下dao 层相同的
包，然后将sql 映射文件(xxxMapper.xml文件)拷贝到该包下即可。