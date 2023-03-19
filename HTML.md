# html

#### HTML概述：

- HTML 是用来描述网页的一种语言。
- HTML 指的是超文本标记语言 (Hyper Text Markup Language)
- 超文本就是指页面内可以包含图片、链接，甚至音乐、程序等非文字元素
- HTML 不是一种编程语言，而是一种**标记语言 (markup language)**
- 标记语言是一套标记标签 (markup tag)，由一组<>包围的关键字
- HTML网页的后缀名一般为.html
- HTML 使用标记标签来描述网页

#### 常用的标签：

* 标题标签h1-h6
* 段落标签 ：<p></p>标签
* 换行标签：</br>（单标签）
* 无序列表标签    <ul> <li></li> </ul>（常用）
  * type属性可以修改列表项前面的符号（一般在css中做）
* 有序列表    <ol> <li></li> </ol>（不常用）
  * type可以修改列表项前面的符号
* 加粗标签<b></b>
* 图片标签 <img/> 
  * 所带的属性
    * alt:表示当图片路径错误或者加载不出来的时候用字面信息表示这个图片的内容
    * src:图片的地址（或路径）。这里分为相对路径和绝对路径。
    * width和height:设置图片的宽度和高度
    * (了解) title:鼠标放在图片上时显示的描述性文字
    * ..表示当前文件所在的上一级目录
* 超链接<a href="" target =""></a>
  * href:表示要跳转的路径（这里的路径要写全如：http://www.baidu.com）
  * target属性有两个值啊 _self（默认值）表示当前窗口， _blank表示打开新的窗口
* 分割线：<hr/>

#### 常用的转义字符：

* 空格转义字符
  * 空格字符：&nbsp；&emsp；（首行缩进用）
  * 左尖括号：&lt；
  * 右尖括号：&gt；
  * 版权号：&copy；

#### iframe内联框架：

* 解释：
  * 它可以在以后html页面上，打开一个小窗口，去加载一个单独的页面（就是页面里面再打开一个页面）
* iframe和a标签组合使用的步骤
  * 1 在iframe标签中使用name属性定义一个名称
  * 2 在a标签的target属性上设置iframe的name的属性值

```html
练习：
	<body>
<!--iframe 它可以在以后html页面上，打开一个小窗口，去加载一个单独的页面（就是页面里面再打开一个页面）

    iframe和a标签组合使用的步骤
        1 在iframe标签中使用name属性定义一个名称
        2 在a标签的target属性上设置iframe的name的属性值-->

这是一个页面</br></br>
<iframe src="img标签.html" frameborder="1" width="300" height="300" name="ab"></iframe></br>

<ul>
    <li><a href="img标签.html" target="ab">img标签</a></li>
    <li><a href="Table标签.html" target="ab">Table标签</a></li>
    <li><a href="有序列表和无序列表.html" target="ab">有序列表和无序列表</a></li>
    <li><a href="表格跨行跨列.html" target="ab">表格跨行跨列</a></li>
</ul>

</body>
```

#### table标签

* table是表格标签
  * tr是行标签
  * td是单元格标签
  * th是表头标签具有加粗和居中的效果多用于表中的第一行作为字段名
* align设置单元格的文本对其格式
* cellspacing 设置单元格与单元格之间的距离
* border 设置单元格的边框
* colspan表示自己占几列位置  注意当他占给定的列的时候后面的几列就会被顶出来
*  rowspan表示自己占几行位置  注意当他占两行的时候后面的几行就会被顶出来

#### 表单标签：

* ##### form表单标签

  * action属性设置提交服务器的地址
  * method属性设置提交的方式GET（默认）或POST

* 表单提交的时候，数据没有发送给服务器的三种情况：
           1 表单中没有name属性值 （所有要发送给服务器的内容都要加name属性）
           2 单选、多选 （下拉列表中的option标签）都需要添加value属性  以便发送给服务器
           3 要发送给服务器的内容没有在form表单中

  GET请求的特点是：
           1 浏览器地址栏中的地址是：action属性+[？+请求参数] (中括号中表示可选的 可有可无)
               请求参数的格式是：name=value&name=value...&name=value
           2 提交的信息不安全都是明文发送的
           3 有长度限制（不能超过100（这里根据浏览器不同所限制的字符数量也不同，api中说的是100）个ASCII字符，
           如果超过了那么100以后的内容不会发送）

  POST请求的特点：
           1 浏览器地址栏中只有action属性值（也就是只有服务器地址）
           2 相对于get请求较为安全
           3 理论上没有长度限制

* ##### form表单项

  * input type = text 是文件输入框        value是默认显示内容    	placeholder=“ ”双引号中的内容就是我们常看到的请输入用户名
  *  input type = password 是密码输入框      value是默认显示内容
  *   input type = radio 是单选框          checked = checked是默认选中   当像服务器提交时还要加value属性

*  注意：单选标签如果不给他们赋值相同的name名称的话所有的单选框都可以选，如果两个或多个单选框同时赋值了一个相同的name名那么就只能选择一个
  * input type="checkbox" 是复选框，复选框是可以多选的    checked = checked是默认选中   当向服务器提交数据时 input中要有name属性和value属性  name代表相同的名字（所有复选框的名字必须一样） value代表传输的值
  * select 是下拉列表框
    * option是下拉列表框中的选项 如果没有设置默认选中那么默认就是列表中的第一个     selected="selected"是设置默认下拉列表框中的选项

* 注意<select>   <option></option>     </select>   中如果向服务器传数据的话跟input中需要name不同  它可以默认传入的是option中的内容，如果加了value=“ ”那就向服务器中传入value的值

  * textarea  表示多行文本输入框
    *  rows 表示可以显示几行高度的文字（超出了就要下拉滚动条才能够显示出来）
    *  cols 表示一行能最多输入几个字符

*  注意：这时的默认值不是value 而是起始标签和结束标签中间的内容才是默认值

  * input type="reset"  重置          value属性可以更改内容（就是把重置两个字改成你想要的字符）
  * input type="submit"  提交       value属性可以更改内容（就是把提交两个字改成你想要的字符）
  * input type="button"  是个按钮默认按钮上无值      value属性可以添加按钮上的内容
  * input type="file"  选择一个文件当点击提交按钮时会提交这个文件
  * input type="hidden" 隐藏域   当我们要发送某些信息，而这些信息又不需要用户看到时就使用隐藏域（提交的时候同时发送给服务器）这个隐藏域是给程序用的

```html
提交一个表单的练习：
<form action="http://www.baidu.com" method="post">
    <input type="hidden" name="action" value="123">
    <h1 align="center">用户注册</h1>
    <table align="center">
        <tr>
            <td>用户名:</td>
            <td><input type="text" value="username" name="username"/></td>
        </tr>
        <tr>
            <td>用户密码:</td>
            <td><input type="password" value="root" name="psaaword"/></td>
        </tr>
        <tr>
            <td>性别:</td>
            <td><input type="radio" name="sex" checked="checked" value="boy"/>男
                <input type="radio" name="sex" value="girl"/>女
            </td>
        </tr>
        <tr>
            <td>兴趣爱好:</td>
            <td>
                <input type="checkbox" checked="checked" name="hobby" value="java"/>Java
                <input type="checkbox" checked="checked" name="hobby" value="h5"/>H5
                <input type="checkbox" name="hobby" value="js"/>javascript
            </td>
        </tr>
        <tr>
            <td>国籍:</td>
            <td>
                <select name="coutry">
                    <option value="none">----请选择国籍----</option>
                    <option selected="selected" value="cn">中国</option>
                    <option value="usa">美国</option>
                    <option value="jp">日本</option>
                    <option value="kor">韩国</option>
                </select>
            </td>
        </tr>
        <tr>
            <td>自我评价:</td>
            <td><textarea rows="10" cols="20" name="desc">请输入自我评价的内容</textarea></td>
        </tr>
        <tr>
            <td><input type="reset"/></td>
            <td align="center"><input type="submit"/></td>
        </tr>
        <tr>
            <td><input type="button" value="自定义按钮"/></td>
        </tr>
    </table>

</form>
```

