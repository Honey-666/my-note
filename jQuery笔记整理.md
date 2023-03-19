# jQuery

#### jquery特点：

* WEITE LESS,DO MORE
* 轻量级（只有几十kb）
* 兼容多个浏览器
* 免费、开源

#### jQuery简介

* $和$()          $代表引用$()代表调用这个函数
  * 源码解析：window.$ = jquery
  * jQuery = function(){}
  * $=jQuery【jQuery是函数引用】

#### $()核心函数的四大作用

* 传入为函数时

  * 类似于window.onload

* windiws.onload与$(fn)的区别

  * 执行时机

    * window.onload当前文档完全加载后在执行
    * $()当前文档绘制完成后执行

  * 当前文档书写次数

    * window.onload只能书写一次（书写多次不会报错，后面覆盖前面）
    * $()可以书写多次

  * 语法结构

    * window.onload只有一种

    * $()有两种写法

      * $()和$(document).ready(function(){

        ​	alert();

        })

* 传入为选择器字符串时

  * 例$("id对象或calss对象或标签名")

* 传入参数为HTML字符串时

  * 创建元素对象（也就是创建标签对象）
  * 例：var $li = $("<li>深圳</li>");

* 传入DOM对象时

  * 如何区分DOM对象和jquery对象
    * 将对象放入alert（对象），object Object,是jQuery对象，object HTMLXXX，是DOM对象

#### DOM和jQuery的相互转换

* DOM对象转换jQuery对象
  * $(DOM对象);
* jQuery转换为DOM
  * 将jQuery看作数组，使用下表    例：alert($div.[0].innerText);

#### 基本选择器

* #ID选择器   $("#id")
  * 选中id相对应的节点
* element选择器   $("标签名")
  * 标签选择器，选择同一种标签的所有结点
* class类选择器     $(".class")
  * 选择类名相同的节点
* 全部选择器     $("*")
* 并列选择器   $("sel1,sel2,sl3...sln")
  * 可以选择几个不同类型的节点如id、类、标签

#### 层级选择器

* $("E  F")后代选择器
  * 找E后代所有的F
* $("E> F")子代选择器
  * 找E子一代的F（子一代）
* $("E+F")相邻选择器
  * 找E后面相邻的F
* $("E~F")兄弟选择器

#### 过滤选择器：基本

* 获取第一个元素  $("E:first")
  * 获取E标签的第一个元素
* 获取第一个元素  $("E:last")
  * 获取E标签的最后一个元素
* 查找所有未选中的元素  $("E:not(:checked)")
  * 查找所有E类型中未选中的选择器
* 匹配所有索引值为偶数的元素，从 0 开始计数   $("E:even")
  * 查找E中索引为偶数的元素（注意是索引）
* 匹配所有索引值为奇数的元素，从 0 开始计数    $("E:odd")
  * 查找E中索引为奇数的元素（注意是索引）
* 匹配一个给定索引值的元素   $("E:eq(要比较的索引)")
  * 查找E中eq中索引的元素   （注意是索引）也就是返回eq中对应索引的标签
* 匹配所有大于给定索引值的元素   $("E:gt(要比较的索引)")
  * 查找E中大于gt中索引的节点
* 匹配所有小于给定索引值的元素   $("E:lt(要比较的索引)")
  * 查找E中小于lt中索引的节点
* 匹配所有标题元素   $(":header")
  * 注意：  ：前无值  找到所有h标题节点
* 选择当前正在执行动画的所有元素  $(":animated")

#### 过滤选择器：内容

* 匹配包含指定文本的元素     $(:contains(‘text’))
* 包含指定选择器的1  $(E:has(标签名))
* 匹配所有不包含子元素或者文本的空元素（也就是匹配没有子元素的空标签）   $(”E:empty”) 
* 匹配含有子元素或者文本的元素，感觉与上面讲的”:empty”形成反义词      $(”td:parent”) 

#### 过滤选择器：可见性

* 匹配所有的不可见元素   $(”E:hidden”) 

  [^注意:input 元素的 type 属性为 “hidden” 的话也会被匹配到.意思是css中display:none和input type=”hidden”的都会被匹配到]: 

* 匹配所有的可见元素    $(”E:visible”)  

#### 过滤选择器：属性

* #### 1）  [attribute]

  - 用法: $(”div[id]“) 
  - 说明: 匹配包含给定属性的元素。例子中是选取了所有带id属性的div标签。

  #### 2）  [attribute=value]

  - 用法: $(”input[name='newsletter']“).attr(”checked”, true)
  - 说明: 匹配给定的属性是某个特定值的元素。例子中选取了所有name属性是newsletter的 input 元素。

  #### 3）  [attribute!=value]

  - 用法: $(”input[name!='newsletter']“).attr(”checked”, true)。   
  - 说明：匹配所有不含有指定的属性，或者属性不等于特定值的元素。此选择器等价于:not([attr=value])，要匹配含有特定属性但不等于特定值的元素，请使用[attr]:not([attr=value])。之前看到的 :not 派上了用场。

  #### 4）  [attribute^=value]

  - 用法: $(”input[name^=‘news’]“)
  - 说明: 匹配给定的属性是以某些值开始的元素。我们又见到了这几个类似于正则匹配的符号。现在想忘都忘不掉了吧？！

  #### 5 ）  [attribute$=value]

  - 用法: $(”input[name$=‘letter’]“)
  - 说明: 匹配给定的属性是以某些值结尾的元素。

  #### 6）   [attribute*=value]

  - 用法: $(”input[name*=‘man’]“)
  - 说明: 匹配给定的属性是以包含某些值的元素。

  #### 7） [][][][][selector1] [selector2] [selectorN]

  - 用法：$("input[id] [name$='man']]")
  - 说明：复合属性选择器，需要同时满足多个条件时使用。

#### 过滤选择器：子元素

#### 1）   :nth-child(index/even/odd/equation)(了解)

- 用法: $(”ul li:nth-child(2)”) 

- 说明: 匹配其父元素下的第N个子或奇偶元素.这个选择器和之前说的基础过滤(Basic Filters)中的 eq() 有些类似，不同的地方就是前者是从0开始,后者是从1开始。

#### 2）   :first-child

- 用法: $(”ul li:first-child”) 

- 说明: 匹配第一个子元素。**’:first’ 只匹配一个元素，而此选择符将为每个父元素匹配一个子元素。**这里需要特别的记忆一下区别。二者的调用者都是针对要操作的元素来讲的。

#### 3）   :last-child

- 用法: $(”ul li:last-child”)

- 说明: 匹配最后一个子元素。’:last’只匹配一个元素，而此选择符将为每个父元素匹配一个子元素。

#### 4）   : only-child(了解)

- 用法: $(”ul li:only-child”)

- 说明: 如果某个元素是父元素中唯一的子元素，那将会被匹配。如果父元素中含有其他元素，那将不会被匹配。意思就是：只有一个子元素的才会被匹配！

#### 过滤选择器：表单

#### 1）   :input

- 用法: $(”:input”)  
- 说明:匹配所有 text, textarea, select 和 button 元素 。

#### 2）   :text

- 用法: $(”:text”) 
- 说明: 匹配所有的单行文本框。

#### 3）   :password

- 用法: $(”:password”) 
- 说明: 匹配所有密码框。

#### 4）  :radio

- 用法: $(”:radio”) 
- 说明: 匹配所有单选按钮。

#### 5）   :checkbox

- 用法: $(”:checkbox”) 
- 说明: 匹配所有复选框。

#### 6）   :submit

- 用法: $(”:submit”) 
- 说明: 匹配所有提交按钮。

#### 7）  :image

- 用法: $(”:image”) 
- 说明: 匹配所有图像域。

#### 8）  :reset

- 用法: $(”:reset”) 

- 说明: 匹配所有重置按钮。

#### 9）  :button

- 用法: $(”:button”) 

- 说明: 匹配所有按钮.这个包括直接写的元素button。

#### 10）  :file

- 用法: $(”:file”) 

- 说明: 匹配所有文件域。

#### 11）  :hidden

- 用法: $(”input:hidden”) 
- 说明: 匹配所有不可见元素，或者type为hidden的元素.这个选择器就不仅限于表单了,除了匹配input中的hidden外,那些style为hidden的也会被匹配。

#### 过滤选择器：表单对象属性

- 此选择器主要对所选择的表单元素进行过滤

#### 1）   :enabled

- 用法: $(”input:enabled”)

- 说明: 匹配所有可用元素.意思是查找所有input中不带有disabled=”disabled”的input.不为disabled,当然就为enabled啦。

#### 2）   :disabled

- 用法: $(”input:disabled”)

- 说明: 匹配所有不可用元素.与上面的那个是相对应的。

#### 3）   :checked

- 用法: $(”input:checked”)
- 说明: 匹配所有被选中的元素(复选框、单选框等，不包括下拉列表select中的option)。
  - 注意：官方文档说明是不包括select中的option，但测试中是包含的。只是不建议大家这样用。

#### 4）   :selected

- 用法: $(”select option:selected”)

- 说明: 匹配所有选中的option元素。