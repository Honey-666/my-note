# Ajax

#### 简介

* ajax的实际意义就是，不发生页面体跳转，也能发送请求响应
* 相当于通过Js的方式向服务器发送异步请求（不刷新浏览器），响应数据（如何响应的，里面有回调函数）

#### 同步请求和异步请求

##### 同步请求

* 请求方式
  * 超链接
  * form表单
  * location
* 缺点
  * 发送请求，做出响应，如果响应之前，整个页面无法访问（一般指页面卡了，用户体验差）
  * 即使更改少量的数据，也要刷新页面才会显示

> **使用场景：大量更改代码时还是使用同步请求**

##### 异步请求（也叫局部请求）

* 请求方式：ajax
* 弥补同步的不足

#### 通过Jquery的方式向Ajax请求

**Hello**：

```javascript
$.ajax({
	type:"post"  //请求方式
	URl:"Servlet"  //URL（也就是servlet）
	data:"name=admin"  //参数
dataType:"json"   //期望返回的类型
	success: function(msg){//请求成功后的回调函数。
                    alert( "Data Saved: " + msg ); 
	error: function(){//请求失败后的回调函数。
                    alert( "失败函数" ); 
});
```

#### Ajax的简写方式:

$.get(url,data,success,dataType);

示例：

```javascript
$.get("${pageContext.request.contextPath}/AjaxServlet",{"stuName":"yinhang","stuAge":20},function(msg){
                alert( "年龄: " + msg.stuAge);
                alert( "姓名: " + msg.stuName);
            },"json");
```

$.post(url,data,success,dataType)

示例：

```javascript
 $.post("${pageContext.request.contextPath}/AjaxServlet",{"stuName":"yinhang","stuAge":20},function (msg) {
                alert("年龄"+msg.stuAge);
                alert("姓名"+msg.stuName);
            },"json");
```

$.getJSON(url,data,success);（直接将Json写到了前面）

示例：

```javascript
 $.getJSON("${pageContext.request.contextPath}/AjaxServlet",{"stuName":"yinhang","stuAge":20},function (msg) {
                alert("年龄" + msg.stuAge);
                alert("姓名" + msg.stuName);
            });
```

# JSON

#### 简介

* json 是js提供的一种轻量级的数据交换格式
* 本质上就是一个js对象，但是这个对象比较特殊，可以直接与String相互转换
* json用于在不同语言及不同生态之间进行数据传递

#### 语法

* **var jsonObj = {"key":value,"key2":value2........"keyn":valuen}；**    
*  json的key建议使用string类型，json使用js支持的类型即可

示例：

```javascript
var jsons = {
    "key1":"abc", // 字符串类型
    "key2":1234,  // Number
    "key3":[1234,"21341","53"], // 数组
    "key4":{                    // json类型
        "key4_1" : 12,
        "key4_2" : "kkk"
    },
    "key5":[{                  // json数组
        "key5_1_1" : 12,
        "key5_1_2" : "abc"
    },
        {
            "key5_2_1" : 41,
            "key5_2_2" : "bbj"
        }]
};

// alert(jsons.key2);          //1234
// alert(jsons.key3[2]);        //53
// alert(jsons.key4.key4_2);    //kkk
alert(jsons.key5[1].key5_2_1) //41
```

#### Json的使用

* 使用场景：json用于在不同语言及不同生态之间进行数据传递

* 在js语言环境下，json对象与jsonString转换问题

  * jsonObj-------》jsonString
    * JSON.stringify（json），此方法是将json对象转换为json字符串
  * jsonString-------》jsonObj
    * var jsonObj = JSON.parse( jsonString )：此方法可以把一个json字符串转换成为json对象

* 在java语言环境下，java对象【bean|List|Map】与jsonString转换问题（使用google的第三方jar包中的两个方法）

  * Java对象---------》jsonString
    * String jsonString = gson.toJson(obj);  将java对象转换为json字符串
  * jsonString---------》Java对象
    * Object obj = gson.fromJson(String jsonString,Object.class);
    * Object<T> obj = gson.fromJson(String jsonString,Type);

  示例：

  ```java
      Gson gson = new Gson();
  
      /**
       * JavaBean <->  jsonString
       */
      @Test
      public void testJavaBeanToJsonString(){
          Teacher sha = new Teacher(28,"laosha",100.0);
          //javaBean->jsonString          //String s = "{"age":28,"name":"laosha","salary":100.0}";
          String s = gson.toJson(sha);
          System.out.println("s = " + s);
  
          //jsonString->javaBean
          Teacher teacher = gson.fromJson(s, Teacher.class);
          System.out.println("teacher = " + teacher.getName());
      }
  
      /**
       * List <->  jsonString
       */
      @Test
      public void testListToJsonString() {
          List<Teacher> list = new ArrayList<>();
  
          list.add(new Teacher(28,"laoshi",100.0));
          list.add(new Teacher(40,"kangshifu",100.0));
          list.add(new Teacher(40,"malaoshi",100.0));
  
          //list->jsonString              String s = "{{},{},{}}";  String s = "[{},{},{}]";
          String s = gson.toJson(list);
          System.out.println("s = " + s);
  
          //jsonString->list
  //        List list1 = gson.fromJson(s, List.class);             //错误的
  //        System.out.println("list1 = " + ((Teacher)list1.get(0)).getName());
  
          List<Teacher> list1 = gson.fromJson(s, new TypeToken<List<Teacher>>() {
          }.getType());
  
          System.out.println("list1:" + list1.get(0).getName());
      }
  
      /**
       * Map <->  jsonString
       */
      @Test
      public void testMapToJsonString() {
          Map<String,Teacher> map = new HashMap<>();
          map.put("ls",new Teacher(28,"laoshi",100.0));
          map.put("ff",new Teacher(18,"ff",100.0));
          map.put("cc",new Teacher(18,"cc",100.0));
          //map->jsonString               String s = "{"":{},"":{},...}";
          String s = gson.toJson(map);
          System.out.println("s = " + s);
          //jsonString->map
          Map<String,Teacher> map1 = gson.fromJson(s,new TypeToken<Map<String,Teacher>>(){}.getType());
          System.out.println(map1.get("ff").getAge());
      }
  ```