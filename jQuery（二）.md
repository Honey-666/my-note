# jQuery（二）

## jQuery操作DOM

#### 文档处理：过滤（文档处理）

* **eq(index)**
  
* 例：$("p").eq(1)   查找p标签元素的索引为1的标签和内容，如果为负数则是返回索引为0的
  
*  **filter(expr）**（过滤器）(重要filter里面可以传任意的筛选)
  
  * 例：$("p").filter(".selected")  查找p标签中.selected类名的p标签
  
* **children([expr])**
  
* 例：$("div").children()  查找div中的所有元素的子元素
  
*  **parent([expr]) **     解释：取得一个包含着所有匹配元素的唯一父元素的元素集合
  
  * 例：$("p").parent()          返回p的父元素
  
* **parents([expr])**
  
* $("span").parents("p")   找到每个span的所有是p元素的祖先元素。	
  
* **find(expr）**

  * 例：同ancestor descendant相同   返回匹配给定选择器的后代元素
  *  $("p").find("span")  查找p标签的后代元素span

* **$(selector)**

  * 返回在jQuery中添加的标签对象

  ```html
  $("ul")
    .append("<li>" + $("ul").selector + "</li>")
    .append("<li>" + $("ul li").selector + "</li>")
    .append("<li>" + $("div#foo ul:not([class])").selector + "</li>");
    结果:
  	ul
  	ul li
  	div#foo ul:not([class])
  ```

* **each(callback)**
  
  * 遍历jQuery对象包含的数据
  
* **next()**

* **nextAll（）**

  * 类似于E~F

* **prev()**  

  * 上一个元素

* **siblings()**

  * 查找所有的兄弟元素（包括前面和后面）

#### 获取长度

* ##### **属性：length**

  * jQuery 对象中元素的个数。

* ##### **函数：size()**

  * jQuery 对象中元素的个数。与length属性作用一样。

#### 内部插入节点（文档处理）

* **append(content)** （内部后置就是放到标签内容的后面）
  
  * 向每个匹配的元素的内部的结尾处追加内容。
* 例：$("p").append("**<b>Hello</b>**");   在p标签的内容末尾处添加**<b>Hello</b**>
  
*  **appendTo(content)** 

  - 把所有匹配的元素追加到另一个指定的元素集合中

  - 例：

    ```html
    <p>I would like to say: </p>
    <div></div><div></div>
    
    $("p").appendTo("div");      把<p>I would like to say: </p>插入到div中
    ```

*  **prepend(content)**    （内部前置就是放到标签内容的前面）

  - 向每个匹配的元素的内部的开始处插入内容（即把传入的东西插入到标签内容的最前面）
  - 例：$("p").prepend("**<b>Hello</b>**");    把**<b>Hello</b>**插入到p标签内容的最前面

*   **prependTo(content)** 

  - 把一个指定的元素追加到另一个元素中
  - 例：$("p").prependTo("#foo");           把所有段落p标签追加到ID值为foo的元素中。

#### 外部插入节点（文档处理）

* after(content) （外部后置就是放到标签后面）
  * 在每个匹配的元素之后插入内容 
  * 例：$("p").after("<b>Hello</b>");  将<b>Hello</b>插入到p标签后面
* before(content)

  * 在每个匹配的元素之前插入内容 （注意是插入在标签之前，不是 插入在内容之前）
  * 例：$("p").before("<b>Hello</b>");     将<b>Hello</b>插入到p标签的前面
* insertAfter(content)（外部前置就是放到标签前面）

  * 把所有段落插入到一个元素之后。与 $("#foo").after("p")相同
  * 例：$("p").insertAfter("#foo");       将p标签连带内容插入到id为#foo的div标签后面   结果：<div id="foo">Hello</div><p>I would like to say: </p>
*  insertBefore(content) 

  * 把所有段落插入到一个元素之前。与 $("#foo").before("p")相同。
  * 例：$("p").insertBefore("#foo");  把p标签连带内容放到#foo的div标签之前   结果：<p>I would like to say: </p><div id="foo">Hello</div>

#### 创建节点

#### 删除节点（文档处理）

* empty()
  * 删除匹配的元素集合中所有的子节点(不包括本身)。（类似于掏空这个词语）

  * 例：

    ```html
    把所有段落的子元素（包括文本节点）删除：
    <p>Hello, <span>Person</span> <a href="#">and person</a></p>
    
    $("p").empty();
    
    结果：<p></p>
    ```

*  remove()
  * 删除匹配的元素及其子元素(包括本身)
  * 例：<p>Hello</p> how are <p>you?</p>         $("p").remove();   结果：how are   这里将所有的p标签和p标签里面的内容全部删除了

#### 修改节点（文档处理）

* replaceAll(selector)
  * 用匹配的元素替换掉所有 selector匹配到的元素。

  * 例：

    ```htnl
    把所有的段落标记替换成加粗标记
    HTML代码：<p>Hello</p><p>cruel</p><p>World</p>
    
    jQuery代码：$("<b>Paragraph. </b>").replaceAll("p");
    
    结果：<b>Paragraph. </b><b>Paragraph. </b><b>Paragraph. </b>
    ```

格式：$("想要替换后标签及内容").replaceAll("原来要被替换的标签及内容")

* replaceWith(content|fn)

  * 将所有匹配的元素替换成指定的HTML或DOM元素。

  * 例：

    ```html
    把所有的段落标记替换成加粗的标记。
    HTML代码：<p>Hello</p><p>cruel</p><p>World</p>
    
    jQuery代码：$("p").replaceWith("<b>Paragraph. </b>");
    
    结果：<b>Paragraph. </b><b>Paragraph. </b><b>Paragraph. </b>
    ```

    格式：$("原来要被替换的标签及内容").replaceAll("想要替换后标签及内容")

#### HTML代码/文本/值（jQuery操作DOM文本）

* **html()**     它可以设置和获取起始标签和结束标签中的内容。跟dom 属性innerHTML 一样。
  * 例：$(“div).html()    获取div标签中的内容(这里获取是可以获取到里面的标签的效果的)，            注意html() 内不传参数是获取，传递参数是设置
  * 例设置： $("div").html(<h1>在div中设置一个标题</h1>)   这里就是设置
* **text()**      它可以设置和获取起始标签和结束标签中的文本。跟dom 属性innerText 一样。
  * 不传参数是获取，传递参数是设置     仅能获取到文本信息 ，如果是传递参数设置那么设置的都为文本
* **val()**         它可以设置和获取**表单项**的value 属性值。跟dom 属性value 一样
  * 不传参数是获取，传递参数是设置   
  * 传递多个值        val(["选项值1,选项值2,…,选项值n"]) 设置单选框、多选框或下拉列表被选中的值
  * 一次性选中单选框、多选框、多选下拉列表、单选下拉列表
    * $(":radio（单选框标识）,:checkbox（复选框标识）,多选框标识，单选下拉列表标识).val(["选项值1,选项值2,…,选项值n"])(框内的值没有顺序要求)
    * 注意：val的里的值只有一个时也要加上[]数组括号

#### 属性（jQuery操作DOM属性）

* **attr(name ,[value])**    可以获取和修改属性值，传一个参数代表获取，传两个参数代表修改

```html
$(":checkbox").attr("checked"); 这里返回的checked的值是undefined	（操作checked、readOnly、selected、disabled等会出现undefined）
```

*  注意：如果复选框没有给定默认值checked=checked，那么如果受用attr获取复选框的checked值就会值undefined，官方觉得返回undefined是错误的
* **prop（）**  同上面attr一样，等于官方对attr的升级修复了undefined的情况，返回的是false

**总结：当操作checked、readOnly、selected、disabled等时使用prop，其他情况使用attr**

* **removeAttr(name)**
  * 删除传入的属性名，name形参代表传入的属性名

#### 样式（jQuery操作样式表）

* css()

  * 一个值是取值，两个值是赋值

* addClass(className)

  - 添加class属性  （使用情况：提前给出了class的css样式，当使用的时候创建一个clas属性）

  - 例：

    ```html
    $("#myDiv").mouseover(function () {
    			$(this).addClass("bg_c");
    		}).mouseout(function () {
    			$(this).removeClass("bg_c");
    		});
    		这里是使用addClass创建了鼠标移入移出的效果
    bg_c：提前给定的css属性
    ```

* removeClass()

  - 移除class属性

#### jQuery常用事件

* ready(function)：当前文档绘制完成后执行（出发函数）
* click（function）：单击事件
* live（"事件名"，function）：事件委派
* delegate（"选择器",""事件名"，function"）：live事件的升级版
* change("事件名")：文本改变且失去焦点事件
* mouseover:鼠标移入（悬停）事件
* mouseout:鼠标移出事件

#### live和click的区别

* click  对后添加的单击事件无法使用
* live   jQuery 给所有匹配的元素附加一个事件处理函数，即使这个元素是以后再添加进来的也有效
  * 格式：live("click","function函数")

#### 冒泡事件的取消

**描述**：当点击子标签时父标签也会响应

**解决办法**：在子标签中添加 return false；

#### 正则表达式

##### 为什么使用正则表达式

* 传统方式验证表单中数据时，只能验证基本的非空规则，如果验证比较复杂的规则的话，代码难度及代码量较大，使用正则可以避免以上情况

##### 正则语法

* var reg = /规则/;(官方给定的规则)
* var reg = /^ [规则] {n,m}$/;    n,m:表示长度区间从n开始到m结束  ，{n, }:表示最少n位，最多不限   ，{n}：表示长度只能是n位                           （实战中用到的规则）
  * 使用test（）验证正则表达式
* ^表示正则表达式开始
  * 例：^a表示以a开始
* $表达式正则表达式的结束
  * 例：$a表示以a结束

##### 正则字符的表达语法规则

* \d  表示数字0-9
* \w   表示大小写a-z、0-9和_下划线
* .（点）  表示代表除了转移字符\n外所有的字符  
  * 注意如果要使用  .（点）就是使用转义字符\    写成   **\.(杠点)**

##### 正则特殊字符代表的次数

* *，代表{0，} 可以没有可以有多次
* +，代表{1，} 最少一次 或多次
* ？ 代表{0，1} 可以没有或只能有一次