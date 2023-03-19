# JavaScript

#### 特性

* js是解释性语言（边执行边解释）
* js是面向对象的
* 弱类型语言(js定义变量或函数时全用var)
* js是动态的语言（java是静态型语言，先编译后运行）

#### JavaScript的组成

* 由ECMAScript（标准）、DOM（文档对象模型）、BOM（浏览器对象模型）组成

#### JavaScript的三种运行位置

* 行内
* 内部
* 外联\<script type = "text/javascript" src = "js的文件名">\</script>   注意标签内的内容都要写在js文件中，写在标签内不会执行（因为有src属性，写入是无效的）

#### 变量

定义： var  数据名 = 数据值；

js的数据类型：

* 基本数据类型
  * number
  * string（js中string时小写）
  * boolean
  * null（当判断null的类型时他是objec类型）
  * undefined
* 对象数据类型
  * 数学组
  * 函数
  * 一般对象
* 注意使用typeof（要查看的数据是什么类型的参数）可以验证js变量的数据类型
* 注意2 ：所有只声明未赋值都是undefined  只有当赋值时才能确定数值的类型

#### 运算符

* 类似于Java

* js中==表示内容相等
* ===表示内容和类型都相等

#### 数组

* 定义：var 数组名 = [ ];  或  var  数组名 = new array();
* 数组中可以存储任意类型的数据
* js中的数组会自动扩容
* 数组的遍历中定义变量都用var （没有int 类型）

#### 函数

* 声明格式

  * 普通函数：function  函数名(形参列表){

    ​	函数体

    }

  * 匿名函数：function (){

    函数体

    }

    匿名函数2：

    var fun = function (){

    函数体

    }

* 注意事项：js中调用函数时，不检查形参与实参的匹配情况（如果形参<实参，那么运行结果只取实参和形参相对应的前几个）

* 当形参>实参时

  * 实参数据类型时number：返回NaN（not a number）
  * 实参数据类型时string时以拼接的方式返回

* js中不存在发放的重载如果声明两个相同名字的函数那么后面的函数就会把前面的给覆盖

* arguments函数的隐形参数类似于Java中的可变形参

  * arguments[这里查找任意的参数索引]；（获取多余的实参）

#### 对象

* 数组是对象

* 函数是对象

* 内置对象

  * Array、Date、Math
  * document、body、button
  * window（BOM核心对象）、location
    * window.alert()是一个提示框
    * window.confirm()提示框提示确定是否删除
  * 实战中通常省略window
    * location控制地址栏中的地址，location.href = “里面是URL ”相当于超链接的功能，通常href可以省略

* 一般对象

  * 定义：

  * 一：var obj = new Object（）;

  * 二：var  jsonObj = {

    属性名：属性值，

    属性名：属性值，

    ...

    属性名：属性值

    }；

  * 对象的使用

    * 对象名.属性或函数（this同java一样代表当前对象）

#### 事件

* onload：加载事件
* onclick：单击事件
* onchange：文本改变且失去焦点事件
* onsubmit：提交事件（点击提交按钮时会有两个事件，第一个是单击事件，第二个是提交表单事件）
* onblur：失去焦点事件（就是鼠标点击会有闪烁就是聚焦，点空白就是失去焦点）

注意：当将函数名赋值给事件时时关联事件，如果使用函数名（）的方式赋值给事件是调用这个函数

* window.onload
  * 表示当前文档完全加载后才会执行（就是把整个html文档加载完才会一步一步的去执行）

#### DOM【document object model】

- document对象是window对象的一个属性，代表当前HTML文档，将这个文档抽象成了document对象。
- document对象管理了所有的HTML文档内容。
- 它是包含了整个文档的树形结构，有层级关系。
- 它让我们把所有的标签都**对象化**。
- 我们可以通过document访问所有的标签对象。
- 获取document对象的本质方法是：window.document，而“window.”可以省略。

#### DOM查询API

* 基于当前文档查询
  * document.getElementById():通过id获取元素节点
  * document.getElementsByTagName():通过标签名获取元素节点（返回一个Collaction集合（同下面getElementsByName()一样））
  * document.getElementsByName():通过name属性值获取元素节点（返回一个List集合）
    *  document.getElementsByName();是根据 指定的name属性查询返回多个标签对象集合
    *  这个集合的操作跟数组 一样 集合中每个元素都是dom对象
    *  这个集合中的元素顺序是他们在html页面中从上到下的顺序
* 基于元素节点查询 ：
  * 子元素
    * element.childNodes:获取所有子元素（返回数组）
    * element.firstChild:获取第一个元素节点
    * element.lastChild:获取最后一个元素节点
    * element.getElementsByTagName():通过标签名获取元素节点（返回集合）
  * 父元素
    * element.parentNode:获取父元素节点
  * 兄弟元素
    * element.previousSibling:获取上一个兄弟元素节点
    * element.nextSibling:获取下一个兄弟元素节点

#### 修改和读取

- 读取属性值：**元素对象.属性名**
- 修改属性值：**元素对象.属性名=新的属性值**

#### 正则

 // 表示要求字符串中，是否包含字母e
        // var patt = new RegExp("e");
        // var patt = /e/; // 也是正则表达式对象
        // 表示要求字符串中，是否包含字母a或b或c
        // var patt = /[abc]/;
        // 表示要求字符串，是否包含小写字母
        // var patt = /[a-z]/;
        // 表示要求字符串，是否包含任意大写字母
        // var patt = /[A-Z]/;
        // 表示要求字符串，是否包含任意数字
        // var patt = /[0-9]/;
        // 表示要求字符串，是否包含字母，数字，下划线
        // var patt = /\w/;
        // 表示要求 字符串中是否包含至少一个a
        // var patt = /a+/;
        // 表示要求 字符串中是否 *包含* 零个 或 多个a
        // var patt = /a*/;
        // 表示要求 字符串是否包含一个或零个a
        // var patt = /a?/;
        // 表示要求 字符串是否包含连续三个a
        // var patt = /a{3}/;
        // 表示要求 字符串是否包 至少3个连续的a，最多5个连续的a
        // var patt = /a{3,5}/;
        // 表示要求 字符串是否包 至少3个连续的a，
        // var patt = /a{3,}/;
        // 表示要求 字符串必须以a结尾
        // var patt = /a$/;
        // 表示要求 字符串必须以a打头
        // var patt = /^a/;

 		 // 要求字符串中是否*包含* 至少3个连续的a
        // var patt = /a{3,5}/;
        // 要求字符串，从头到尾都必须完全匹配
        // var patt = /^a{3,5}$/;

#### return false

* submit有默认提交表单的效果，使用return false能够组织表单的提交
* 超链接也可以使用retur false用于不跳转超链接地址
* 多用于取消事件