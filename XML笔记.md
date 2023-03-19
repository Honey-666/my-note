# XML

#### 什么是XML

* XML：e**X**tensible **M**arkup **L**anguage  （可扩展标记语言）
* XML 是一种标记语言，很类似 HTML
* XML 仅仅是纯文本。有能力处理纯文本的软件都可以处理 XML。不过，能够读懂 XML 的应用程序可以有针对性地处理 XML 的标签
* **XML没有任何预定义标签，均为自定义标签**。
* XML严格区分大小写

#### 用途

* 框架的配置文件
  * web.xml
* 数据交换
  * Ajax
  * WebService
* 数据存储
  * 保存关系型数据

#### 语法规则

* 可以不写，如果写就要写在第一行,并且前面没有任何其他字符
  * 通常的写法：**\<?xml version="1.0" encoding="utf-8" ?>** 
    * version表示版本，xml目前就一个版本，encoding字符集解码的格式（java统一用UTF-8）
* 只能由一个根标签
* 标签不能嵌套，正常结束
* 严格区分大小写
* 开始标签中，可以自定义添加属性。但，如果有属性就必须有值，且必须加引号
* 标签不能以数字开头

#### XMlDOM4j解析

* 导包
* 创建解析器对象     SAXReader reader = new SAXReader();
* 使用解析器对象调用read（"xml文件路径"）方法，生成DOM对象
* 通过文档对象的getRootElement()方法获取文档的根标签对象
* 在使用跟标签对象调用elements（）方法返回所有子标签的集合对象
* 遍历集合中的标签对象
* 在遍历中调用attributeValue("属性名")方法获取属性值和elementText("子标签名")获取子标签里面的内容

#### XPath解析的方式

* 导包
* 创建解析器对象     SAXReader reader = new SAXReader();
* 使用解析器对象调用read（"xml文件路径"）方法，生成DOM对象
* 通过文档对象调用selectSingleNode("/根标签/要查的属性标签[@属性名='属性值']")返回要查询的信息Element对象，注意这里默认返回的是Element的父类NOde对象，我们通常强转位Element对象
  * selectNodes("/根标签/要查的属性标签")返回List集合
* 调用attributeValue("属性名")方法获取属性值和elementText("子标签名")获取子标签里面的内容