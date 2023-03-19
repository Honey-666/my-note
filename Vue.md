# Vue

## 基础语法

在线笔记：https://note.youdao.com/s/5vP46EPC

- Vue.js 是前端的**主流框架之一**，和Angular.js、React.js 一起，并成为前端三大主流框架！
- Vue.js 是一套构建用户界面的框架，**只关注视图层**，它不仅易于上手，还便于与第三方库或既有项目整合。（Vue有配套的第三方类库，可以整合起来做大型项目的开发）
- 前端的主要工作？主要负责MVC中的V这一层；主要工作就是和界面打交道，来制作前端页面效果；
- 总结：VueJS是前端渐进式框架，让Html和JavaScript无缝的整合，实现了视图和模型的双向数据绑定（MVVM）VueJS是前端渐进式框架，让Html和JavaScript无缝的整合，实现了视图和模型的双向数据绑定（MVVM）

### MVVM

* 第一个M:model（指vue中的data属性）
* 第一个V:view（指div中的数据）
* VM:viewModel（指整个VUE对象）

双向数据绑定图解：

![image-20201223095301397](D:/尚硅谷/笔记整理/typora-user-images/image-20201223095301397.png)

MVVM模式是视图和模型的双向数据绑定，通过ViewModel实现，当View发生变化，Model变化；当Model变化，View也相应的变化

**使用VUE三步：**

1. 引入vue.js
2. 创建一个vue对象，并指定el属性（必须指定，**表示当前vue要控制的区域**）、data属性（**可选,存放的是 el 中要用到的数据**）、methods属性（可选,**方法函数**）
3. 在页面中指定一个vue控制的区域

```html
<boody>

    <div id = app>
         <!--插值表达式-->
    	{{message}}   这里能够输出message中的内容,{{}}中只能写表达式   
    </div>
    
<script>
    <!--其中每个属性用逗号隔开-->
	new Vue({
        <!--要显示的区域的id或class-->
        el:"app"，
        <!--data 属性中，存放的是 el 中要用到的数据，可以理解为对性的属性，为对象的的属性赋值-->
        data:{
        	message:"hello vue"
    }
    });
</script>
    
    </boody>
```

### vue常用指令

* {{}}       差值表达式，能够获取vue对象中的属性值
* v-on 事件  （v-on：也可以写成@）
  * v-on:事件名="方法名(参数)"    因为事件都有一个函数方法，这个方法写在下方的vue对象中的 methods中
  * v-on:click   单击事件
  * v-on:keydown   键盘按下事件
  * v-on:mouseover   鼠标移入事件
  * v-on:keydown.enter   回车登录或弹窗事件
* v-text与v-html  文本
* v-bind：属性
* v-model：绑定表单（用于回显，唯一一个双向数据绑定的指令，只能用于表单类）
* v-for：循环数据
* v-if与v-show：判断

#### 箭头函数知识点：

function内部默认不支持this关键字，要想支持需要使用箭头函数（es6出来的箭头函数）  ()=>这就是箭头函数，个function功能一样还支持this

#### v-html和v-text区别

* v-html和v-text是先将标签内的内容清空，在显示v-html或v-text的内容

* v-html可以解析在vue中html标签

* v-text不可以解析

#### v-bind  属性绑定

v-bind 等同于 冒号   ：

用法：标签内属性前加上v-bind或冒号

v-bind：属性="**里面只支持两种格式，一种是json格式，一种是数组格式**"

例：

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
		<script src="js/vue.min.js"></script>
		<style>
			.red{
				color: red;
			}
			.big{
				font-size: 30px;
			}
		</style>
	</head>
	<body>
		<div id="app">
			<p>尚硅谷</p>
			<p class="red big">尚硅谷</p>
			<p :class="{red:isRed,big:isBig}">尚硅谷</p>
            <!-- 在数组中使用三元表达式 -->
			<p :class="[ok?'red':'','big']">尚硅谷</p>
            <!-- 直接传递一个数组，注意： 这里的 class 需要使用  v-bind 做数据绑定 -->
			<p :class="[showred,'big']">尚硅谷</p>
			<a :href="url">尚硅谷</a>
		</div>
	</body>
	<script>
		var vm = new Vue({
			el:"#app",
			data:{
				isRed:true,
				isBig:true,
				ok:true,
				showred:'red',
				url:"http://www.atguigu.com"
			}
			
		})
	</script>
</html>
```

#### v-model

**v-model是vue中唯一一个双向数据绑定指令，只支持表单元素**（表单元素，有name属性的，就javaweb第一讲的那些）

代码示例：

```html
<body>

    <div id="app">

        <p>
            输入：
            {{username}}
            <input type="text" v-model="username">
        </p>
        <p>
            {{city}}
            城市：
            <select v-model="city">
                <option value="0">北京</option>
                <option value="1">上海</option>
                <option value="2">深圳</option>
                <option value="3">武汉</option>
            </select>
        </p>

        <p>
            {{gender}}
            性别：
            <input type="radio" v-model="gender" value="0">女
            <input type="radio" v-model="gender" value="1">男
        </p>

        <p>
            {{hobby}}
           爱好：
            <input type="checkbox" v-model="hobby" value=0>java
            <input type="checkbox" v-model="hobby" value=1>C
            <input type="checkbox" v-model="hobby" value=2>C++
            <input type="checkbox" v-model="hobby" value=3>Js
        </p>

        <p>
            {{content}}
            描述：
           <textarea v-model="content"></textarea>
        </p>

    </div>


<script>
    new Vue({
        el:"#app",
        data:{
            username:"",
            city:0,
            gender:1,
            hobby:[],
            content:''

        }


    });
</script>
</body>
```

#### v-for

**作用**：遍历

* in表示每次从数组中取一个数据
* 遍历的数据在那个标签内，就会形成n个标签，每条数据对应一个这个标签（如在div中会形成多个div）

**遍历json数据时**

* val    in    vue中的对象  表示遍历json的属性值

* val,key    in   vue中的对象    表示遍历json的属性值和属性名

代码示例：

```html
<div id="app">

<p v-for="data,index in list">{{index}}---------->{{data}}</p>
<p v-for="data,index in obj">{{index}}---------->{{data.age}}</p>
<p v-for="data,index in obj">{{index}}---------->{{data}}</p>


<p v-for="key,index,val in jso">{{index}}---------->{{key}}---------->{{val}}</p>

<!--  迭代数字  遍历1-100-->
<!--    <p v-for="count in 100">{{count}}</p>-->

</div>


<script>
   var vue = new Vue({
       el:"#app",
        data:{
            list:[1,2,3,4,5,6],
            obj:[{name:'zs',age:18},{name:'ls',age:19},{name:'ww',age:20}],
            jso:{
                id:1,
                name:'hh',
                age:18
            }
        }

    });

</script>

</body>
```

#### v-for key的使用

:**key**

```html
<div id="app">
    id:
    <input type="text" v-model="id">
    name:
    <input type="text" v-model="name">

    <input type="button" @click="add()" value="添加">

<!--    :key="obj.id"  解决选中后在上方插入的问题-->
    <p v-for="obj in list" :key="obj.id">
        <input type="checkbox">
        {{obj.id}}--------->{{obj.name}}
    </p>

</div>


<script>
    var vue = new Vue({
        el: "#app",
        data: {
            id: "",
            name: "",
            list: [
                {id: 1, name: '李斯'},
                {id: 2, name: '嬴政'},
                {id: 3, name: '赵高'},
                {id: 4, name: '韩非'},
                {id: 5, name: '荀子'}
            ]
        },
        methods:{
            add:function () {

                // this.list.push( {id: this.id, name: this.name})
                 this.list.unshift( {id: this.id, name: this.name})
            }

        }

    });

</script>

</body>
```

#### v-if与v-show

**作用**：分支语句

**语法：**v-if="判断条件"

* v-if、v-else、v-else-if

**v-if与v-show的区别**

* 当条件不符合的时候v-if的标签被删除了，二v-show是设置样式隐藏了

示例代码：

```html
<body>
<div id="app">

    <p v-if="num > 10">大于十</p>
    <p v-else-if="num > 5">大于五</p>
    <p v-else="num <= 5">小于五</p>

</div>


<script>
   var vue = new Vue({
       el:"#app",
        data:{
            num:7
        }

    });

</script>

</body>
```

### 数组的splice()方法	

splice() 方法向/从数组中添加/删除项目，然后返回被删除的项目。

该方法会改变原始数组。

语法

```
数组名.splice(index,0或要删除的个数,要添加的内容（如果不添加可以不写）)
```

| **参数**        | **描述**                                                     |
| --------------- | ------------------------------------------------------------ |
| index           | 必需。整数，规定添加/删除项目的位置，使用负数可从数组结尾处规定位置。 |
| howmany         | 必需。要删除的项目数量。如果设置为 0，则不会删除项目。       |
| item1, …, itemX | 可选。向数组添加的新项目。                                   |

返回值

| **类型** | **描述**                             |
| -------- | ------------------------------------ |
| Array    | 包含被删除项目的新数组，如果有的话。 |

说明

splice() 方法可删除从 index 处开始的零个或多个元素，并且用参数列表中声明的一个或多个值来替换那些被删除的元素。

如果从 arrayObject 中删除了元素，则返回的是含有被删除的元素的数组。

① 案例一

在本例中，我们将创建一个新数组，并向其添加一个元素：

```html
<script type="text/javascript">

var arr = new Array(6)
arr[0] = "George"
arr[1] = "John"
arr[2] = "Thomas"
arr[3] = "James"
arr[4] = "Adrew"
arr[5] = "Martin"

document.write(arr + "<br />")
arr.splice(2,0,"William")
document.write(arr + "<br />")

</script>
```

输出：

```txt
George,John,Thomas,James,Adrew,Martin
George,John,William,Thomas,James,Adrew,Martin
```

② 案例二

在本例中我们将删除位于 index 2 的元素，并添加一个新元素来替代被删除的元素：

```html
<script type="text/javascript">

var arr = new Array(6)
arr[0] = "George"
arr[1] = "John"
arr[2] = "Thomas"
arr[3] = "James"
arr[4] = "Adrew"
arr[5] = "Martin"

document.write(arr + "<br />")
arr.splice(2,1,"William")
document.write(arr)

</script>
```

输出：

```txt
George,John,Thomas,James,Adrew,Martin
George,John,William,James,Adrew,Martin
```

③ 案例三

在本例中我们将删除从 index 2 (“Thomas”) 开始的三个元素，并添加一个新元素 (“William”) 来替代被删除的元素：

```
<script type="text/javascript">

var arr = new Array(6)
arr[0] = "George"
arr[1] = "John"
arr[2] = "Thomas"
arr[3] = "James"
arr[4] = "Adrew"
arr[5] = "Martin"

document.write(arr + "<br />")
arr.splice(2,3,"William")
document.write(arr)

</script>
```

输出：

```txt
George,John,Thomas,James,Adrew,Martin
George,John,William,Martin
```

### Vue生命周期

共八个，都是成对出现的

- beforeCreate,==created==,
- beforeMount,==mounted==,
- beforeUpdate,updated,
- beforeDestroy,destroyed。

钩子函数：不需要我们声明，和methods并列

created  用的多相当于构造器，负责赋值

### Vue中的ajax

axios相当于jQuery中的ajax

引入axios之前要先引入vue.js

语法：

* axios.get("/请求地址?name=value&name2=value2...")

  请求成功走then，请求失败走catch失败，finley() 必须执行

.then(()=>{

里面是键值对信息

})

.catch(()=>{

})	

* axios.post("/请求地址",{json对象里面都是键值对})

  请求成功走then，请求失败走catch失败，finley() 必须执行

.then(()=>{

里面是键值对信息

})

.catch(()=>{

})	

## 脚手架

### ref的作用

```vue
在普通的html标签上的时候，能够获取dom元素，相当于document.getElementById()
ref在自定义组件上时，能够获取这个组件的vc对象，而document.getElementById()只能获取子组件的内容
```

### props总结：

```vue
1、使用props可以用来父组件给子组件传参，子组件接收参数使用更灵活
2、props用来接收组件传递过来的参数，有三种接收方式，参见Student组件
3、传递参数时数值类型可以使用v-bind来实现
4、传递组件时不能使用vue已经使用的属性类如 key、ref等
5、属性的加载顺序是先加载props接收的值，然后在加载data()函数里面呢值
6、官方不建议改变接收的值，如果要改变可以将接收过来的值赋值给data()函数中自定义的属性，然后操作
data()函数内的属性值来就行修改，渲染时渲染data中的属性即可
```

### mixin(混入):

```vue
局部混入
和Java中的util很相似，可以将公共的方法和数据提取出来，然后使用到的组件分别引用
使用方法：在组件中引入mixin.js，然后配置mixins:[引入的名称]，这里必须为数组，一个js中可以配置多个混入
如果混入的js文件中有和组件相同的方法和数据那么以组件内的数据为准，但是生命周期是都会执行的，并且混入的先执行，组件内的后执行
全局混入:
在main.js使用Vue.mixin(导出的混入名)，如果有多个那么就写多个Vue.mixin(导出的混入名)即可
```

### 插件

```vue
插件的功能是用来增强Vue
本质就是一个install函数，这个函数的第一个参数接收到的是Vue，第二个以后是插件使用者传递的数据(传啥是啥)
使用插件：在main.js中的new Vue({}) 上方导入插件和使用插件，Vue.use(导入的插件名)
```

### 组件中的样式

```vue
<style scoped> scoped表示只在每个组建的局部生效
```

### 组件自定义事件

```vue
定义：一种组件的通信方式，适用于子组件给父组件传递数据
使用场景：A是父组件，B是子组件，B组件想给A组件传递数据，那么就要在A中给B绑定自定义事件(事件的回调在父组件A中)
绑定组件的两种方式：
1、父组件中使用子组件
<Student v-on:sendMsg="getStudentName" @click.native="show"/>
子组件中 触发父组件绑定的自定义事件 this.$emit('sendMsg',传递的参数)
2、使用ref和mounted钩子函数来实现绑定(都在父组件中完成)，这种更灵活，可以使用异步或者是休眠
<Student ref="student"/> mounted中 this.$refs.student.$on('sendMsg', this.getStudentName)
解绑自定义事件：
//解绑一个
this.$off('sendMsg')
//解绑多个
// this.$off(['sendMsg','m1'])
//解绑所有
// this.$off()
组件要使用原生的事件要加 .native 应为vue认为自定义组件的事件都不是原生的事件
```

### 全局事件总线

```vue
定义：一种组件之间的通信方式，适用于任何组件与组件之间通信
安装全局实现总线：
new Vue({
render: h => h(App),
beforeCreate() {
Vue.prototype.$bus = this;  //在vm初始化之前定义全局事件总线，实现各个组件的通信
}
}).$mount('#app')
使用全局事件总线，在想要接受数据的一方的mounted钩子函数中使用如下代码即可
this.$bus.$on('自定义事件名，与发送方保持一致', (params) => {
console.log(params)
})
当然箭头函数也可以改变为 this.xxx，xxx表示的是methods中的函数
切记当组件销毁时，要销毁绑定事件
beforeDestroy() {
//school组件销毁之前把自定义事件解绑，因为school组件虽然销毁了，但是全局事件总线还在，所有绑定事件还在只是没人触发了
this.$bus.$off('hello')
}
```

### 消息的订阅预发布

```vue
第一步：安装发布订阅消息框架 npm i pubsub-js
第二步：在订阅的组件和发布的组件中分别引入
发送消息：pubsub.publish('hello', this.name);
订阅消息： this.pubId = pubsub.subscribe('hello', (param1, param2) => {
  console.log('订阅消息，消息名为', param1, '消息内容为：', param2)
})
解绑消息发布订阅： pubsub.unsubscribe(this.pubId)
```

### $nextTick

```vue
表示DOM更新完之后才会去执行回调函数中的代码，多用在渲染完毕输入框获取焦点
```

### 过度与动画

    详见练习代码.....

### 脚手架配置文件配置代理

```vue
方式一：只能代理一个，而且不能自由切换，详细看代码配置
devServer: {
   proxy: '需要代理的服务器地址'
},
方式二：可自由切换，访问时加前缀就代理不加前缀就不代理，详细看代码配置
devServer: {
    proxy: {
        //前缀，可以自定义
        '/api': {
            //需要代理的地址
            target: 'http://localhost:5000',
            //将多余的前缀替换为空串，重要，不然访问不到服务器
            pathRewrite:{'^/api':''},
            //是否开启websocket 默认是true
            ws: true,
            //是否开启host地址的修改 默认是true
            //意思就是false的话服务器能获取到你的真实地址，true的话服务器获取的地址是和他自己一样的地址，欺骗服务器
            //默认是true
            changeOrigin: true
        },
        //前缀，可以自定义
        '/hello': {
            //需要代理的地址
            target: 'http://localhost:5001',
            //将多余的前缀替换为空串，重要，不然访问不到服务器
            pathRewrite:{'^/hello':''},
            //是否开启websocket 默认是true
            ws: true,
            //是否开启host地址的修改 默认是true
            //意思就是false的话服务器能获取到你的真实地址，true的话服务器获取的地址是和他自己一样的地址，欺骗服务器
            //默认是true
            changeOrigin: true
        }
    }
}
```

### 插槽

> 让父组件可以向子组件指定的位置插入html结构，也是一种组件的通信方式,适用于 父组件==》子组件

### 默认插槽

```vue
在子组件使用slot标签，在父组件中使用组件的组件体内写html标签
例：
	子组件：<slot>我是插槽，这是我的默认数据1</slot>
	父组件：<Category title="美食">
     	  <img src="https:s3.ax1x.com/2021/01/16/srJlq0.jpg" alt="">
   		  </Category>
```

### 具名插槽

```vue
带有名字的slot标签，父组件在使用的时候可以指定要放到那个插槽	
例：
	子组件： <slot name="first">我是插槽，这是我的默认数据1</slot>
   		    <slot name="second">我是插槽，这是我的默认数据2</slot>
	父组件：<!--      指定填充到第一个插槽-->
     	  <img slot="first" src="https:s3.ax1x.com/2021/01/16/srJlq0.jpg" alt="">
     	  <!--      指定填充到第二个插槽-->
      	  <a slot="second" href="http://www.baidu.com">更多美食</a>
```

- 知识补充：
  - 可以在一个插槽中追加内容
  - 多个html元素向一个插槽插入内容可以使用div或template标签提取
  - v-slot:second 相当于slot="second" v-slot:second时2.6版本之后的写法只能在template标签中使用

### 作用域插槽

注意事项：

1. 作用域插槽必须要用template标签包裹
2. scope 接收的是一个对象，对象中有你在插槽中传递的属性  例：{ "games": [ "LOL", "CF", "DNF", "CS.GO" ] }
3. scope="msg" 也可以写成scope="{games}" es6赋值语法将相同的属性赋值
4. slot-scope="{games}"相当于scope="{games}" 也是新的写法



```vue
作用域插槽适用于自定义样式，数据在插槽提供者的组件中的场景，他更加灵活
例：
	子组件：<!--    作用域插槽 传值给插槽的使用者-->
   		  <slot :games="games">我是插槽，这是我的默认数据1</slot>
	父组件：
		<Category title="游戏">
      <template scope="{games}">
        <ol>
          <li style="color: salmon" v-for="(g,index) in games" :key="index">l{{ g }}</li>
        </ol>
      </template>
    </Category>

    <Category title="游戏">
      <template slot-scope="{games}">
        <h4 v-for="(g,index) in games" :key="index">
          {{ g }}
        </h4>
      </template>
    </Category>
```

### Vuex

#### 1.概念

​		在Vue中实现集中式状态（数据）管理的一个Vue插件，对vue应用中多个组件的共享状态进行集中式的管理（读/写），也是一种组件间通信的方式，且适用于任意组件间通信。

#### 2.何时使用？

​		多个组件需要共享数据时

#### 3.搭建vuex环境

1. 创建文件：```src/store/index.js```

   ```js
   //引入Vue核心库
   import Vue from 'vue'
   //引入Vuex
   import Vuex from 'vuex'
   //应用Vuex插件
   Vue.use(Vuex)
   
   //准备actions对象——响应组件中用户的动作
   const actions = {}
   //准备mutations对象——修改state中的数据
   const mutations = {}
   //准备state对象——保存具体的数据
   const state = {}
   
   //创建并暴露store
   export default new Vuex.Store({
   	actions,
   	mutations,
   	state
   })
   ```

2. 在```main.js```中创建vm时传入```store```配置项

   ```js
   ......
   //引入store
   import store from './store'
   ......
   
   //创建vm
   new Vue({
   	el:'#app',
   	render: h => h(App),
   	store
   })
   ```

####    4.基本使用

1. 初始化数据、配置```actions```、配置```mutations```，操作文件```store.js```

   ```js
   //引入Vue核心库
   import Vue from 'vue'
   //引入Vuex
   import Vuex from 'vuex'
   //引用Vuex
   Vue.use(Vuex)
   
   const actions = {
       //响应组件中加的动作
       //收到两个参数，第一个时精简后的$store第二个是你传递过来的参数
   	jia(context,value){
   		// console.log('actions中的jia被调用了',miniStore,value)
   		context.commit('JIA',value)
   	},
   }
   
   const mutations = {
       //执行加
       //收到两个参数，第一个是vue包装过后的state(即原数据提供了get、set方法)第二个是你传递过来的参数
   	JIA(state,value){
   		// console.log('mutations中的JIA被调用了',state,value)
   		state.sum += value
   	}
   }
   
   //初始化数据
   const state = {
      sum:0
   }
   
   //创建并暴露store
   export default new Vuex.Store({
   	actions,
   	mutations,
   	state,
   })
   ```

2. 组件中读取vuex中的数据：```$store.state.sum```

3. 组件中修改vuex中的数据：```$store.dispatch('action中的方法名',数据)``` 或 ```$store.commit('mutations中的方法名',数据)```

   >  备注：若没有网络请求或其他业务逻辑，组件中也可以越过actions，即不写```dispatch```，直接编写```commit```

#### 5.getters的使用

1. 概念：当state中的数据需要经过加工后再使用时，可以使用getters加工。

2. 相当于vue中的计算属性，也要跟vue中的计算属性一样有返回值

3. 在```store.js```中追加```getters```配置

   ```js
   ......
   
   const getters = {
       //能够接收一个state的原始数据，一般用来做对数据的一些处理，相当于vue中的计算属性
   	bigSum(state){
            //也要跟vue中的计算属性一样有返回值
   		return state.sum * 10
   	}
   }
   
   //创建并暴露store
   export default new Vuex.Store({
   	......
   	getters
   })
   ```

4. 组件中读取数据：```$store.getters.bigSum```

#### 6.四个map方法的使用

1. <strong>mapState方法：</strong>用于帮助我们映射```state```中的数据为计算属性

   ```js
   import {mapState} from 'vuex'
   
   computed: {
       // 映射计算属性方式一   ...表示把mapState中的对象属性拆出来放到计算属性的这个对象中（对象写法）
        ...mapState({sum:'sum',school:'school',student:'student'}),
            
       // 方式二 借助mapState生成计算属性：sum、school、student（数组写法）
       ...mapState(['sum','school','student']),
   },
   ```

2. <strong>mapGetters方法：</strong>用于帮助我们映射```getters```中的数据为计算属性

   ```js
   import {mapState, mapGetters} from 'vuex'
   
   computed: {
       //借助mapGetters生成计算属性：bigSum（对象写法）
       ...mapGetters({bigSum:'bigSum'}),
   
       //借助mapGetters生成计算属性：bigSum（数组写法）
       ...mapGetters(['bigSum'])
   },
   ```

3. <strong>mapActions方法：</strong>用于帮助我们生成与```actions```对话的方法，即：包含```$store.dispatch(xxx)```的函数

   ```js
   import {mapState, mapGetters,mapMutations} from 'vuex'
   
   methods:{
       //靠mapActions生成：incrementOdd、incrementWait（对象形式） 注意传参要在上方模板使用出传
       ...mapActions({incrementOdd:'jiaOdd',incrementWait:'jiaWait'})
   
       //靠mapActions生成：incrementOdd、incrementWait（数组形式）
       ...mapActions(['jiaOdd','jiaWait'])
   }
   ```

4. <strong>mapMutations方法：</strong>用于帮助我们生成与```mutations```对话的方法，即：包含```$store.commit(xxx)```的函数

   ```js
   import {mapState, mapGetters,mapMutations,mapActions} from 'vuex'
   
   methods:{
       //靠mapActions生成：increment、decrement（对象形式）  注意传参要在上方模板使用出传
       ...mapMutations({increment:'JIA',decrement:'JIAN'}),
       
       //靠mapMutations生成：JIA、JIAN（对象形式）
       ...mapMutations(['JIA','JIAN']),
   }
   ```

> 备注：mapActions与mapMutations使用时，若需要传递参数需要：在模板中绑定事件时传递好参数，否则参数是事件对象。

**练习代码**

```vue
<template>
  <div>
    <h2>当前求和为：{{ sum }}</h2>
    <h3>放大十倍后的值为：{{ bigSum }}</h3>
    <h3>学校名称是{{ school }},学校地址是{{ student }}</h3>
    <select v-model.number="n">
      <option value="1">1</option>
      <option value="2">2</option>
      <option value="3">3</option>
    </select>
    <button @click="increment(n)">+</button>
    <button @click="decrement(n)">-</button>
    <button @click="incrementOdd(n)">当前和为奇数再加</button>
    <button @click="incrementWait(n)">等一等在加</button>
  </div>
</template>

<script>
import {mapState, mapGetters,mapMutations,mapActions} from 'vuex'

export default {
  name: "Calculate",
  data() {
    return {
      n: 1 //下拉框默认值
    }
  },
  computed: {
    // sum() {
    //   return this.$store.state.sum
    // },
    // school() {
    //   return this.$store.state.school
    // },
    // student() {
    //   return this.$store.state.student
    // },
    // 映射计算属性方式一   ...表示把mapState中的对象属性拆出来放到计算属性的这个对象中
    // ...mapState({sum:'sum',school:'school',student:'student'}),
    //方式二
    ...mapState(['sum', 'school', 'student']),

    // bigSum() {
    //   return this.$store.getters.bigSum
    // },
    //getters映射方式一
    // ...mapGetters({bigSum:'bigSum'})
    //方式二
    ...mapGetters(['bigSum'])
  },
  methods: {
    // increment() {
    //   //操作vuex中的数据
    //   // this.$store.dispatch('jia',this.n)
    //   //因为没有业务逻辑可以直接调用commit
    //   this.$store.commit('JIA', this.n)
    // },
    // decrement() {
    //   //操作vuex中的数据
    //   // this.$store.dispatch('jian',this.n)
    //   //因为没有业务逻辑可以直接调用commit
    //   this.$store.commit('JIAN', this.n)
    // },


    //方法简写形式一,注意传参要在上方模板使用出传
    ...mapMutations({increment:'JIA',decrement:'JIAN'}),
    //方式二
    // ...mapMutations(['JIA','JIAN']),


    // incrementOdd() {
    //   //操作vuex中的数据
    //   // if (this.$store.state.sum % 2) {
    //   //   this.$store.dispatch('jia',this.n)
    //   // }
    //
    //   //将逻辑写道actions中
    //   this.$store.dispatch('jiaOdd', this.n)
    // },
    // incrementWait() {
    //   //操作vuex中的数据
    //   // setTimeout(() => {
    //   //   this.$store.dispatch('jia',this.n)
    //   // }, 500)
    //
    //   //将逻辑写道actions中
    //   this.$store.dispatch('jiaWait', this.n)
    // },

    //方式一
    ...mapActions({incrementOdd:'jiaOdd',incrementWait:'jiaWait'})
    //方式二
    // ...mapActions(['jiaOdd','jiaWait'])
  },
  mounted() {
    // console.log(this)
  }
}
</script>

<style scoped>
button {
  margin-left: 7px;
}
</style>
```

#### 7.模块化+命名空间

1. 目的：让代码更好维护，让多种数据分类更加明确。

2. 修改```store.js```

   ```javascript
   //为每个不同的组件定义不同的actions、mutations、state等方便模块化管理
   const countAbout = {
     namespaced:true,//开启命名空间
     state:{x:1},
     mutations: { ... },
     actions: { ... },
     getters: {
       bigSum(state){
          return state.sum * 10
       }
     }
   }
   //为每个不同的组件定义不同的actions、mutations、state等方便模块化管理
   const personAbout = {
     namespaced:true,//开启命名空间
     state:{ ... },
     mutations: { ... },
     actions: { ... }
   }
   
   const store = new Vuex.Store({
    //定义模块化的$store
     modules: {
       countAbout,
       personAbout
     }
   })
   ```

3. 开启命名空间后，组件中读取state数据：

   ```js
   //方式一：自己直接读取
   this.$store.state.personAbout.list
   //方式二：借助mapState读取：
   //因为store index.js中开启了命名空间，这里可以使用简写形式把每个空间的state属性给读取出来
   ...mapState('countAbout',['sum','school','subject']),
   ```

4. 开启命名空间后，组件中读取getters数据：

   ```js
   //方式一：自己直接读取
   this.$store.getters['personAbout/firstPersonName']
   //方式二：借助mapGetters读取：
   //因为store index.js中开启了命名空间，这里可以使用简写形式把每个空间的state属性给读取出来
   ...mapGetters('countAbout',['bigSum'])
   ```

5. 开启命名空间后，组件中调用dispatch

   ```js
   //方式一：自己直接dispatch
   this.$store.dispatch('personAbout/addPersonWang',person)
   //方式二：借助mapActions：
   //因为store index.js中开启了命名空间，这里可以使用简写形式把每个空间的state属性给读取出来
   ...mapActions('countAbout',{incrementOdd:'jiaOdd',incrementWait:'jiaWait'})
   ```

6. 开启命名空间后，组件中调用commit

   ```js
   //方式一：自己直接commit
   this.$store.commit('personAbout/ADD_PERSON',person)
   //方式二：借助mapMutations：
   //因为store index.js中开启了命名空间，这里可以使用简写形式把每个空间的state属性给读取出来
   ...mapMutations('countAbout',{increment:'JIA',decrement:'JIAN'}),
   ```

### 路由

1. 理解： 一个路由（route）就是一组映射关系（key - value），多个路由需要路由器（router）进行管理。
2. 前端路由：key是路径，value是组件。

#### 1.基本使用

1. 安装vue-router，命令：```npm i vue-router```

2. 应用插件：```Vue.use(VueRouter)```

3. 编写router配置项:

   ```js
   //引入VueRouter
   import VueRouter from 'vue-router'
   //引入Luyou 组件
   import About from '../components/About'
   import Home from '../components/Home'
   
   //创建router实例对象，去管理一组一组的路由规则
   const router = new VueRouter({
   	routes:[
   		{
   			path:'/about',
   			component:About
   		},
   		{
   			path:'/home',
   			component:Home
   		}
   	]
   })
   
   //暴露router
   export default router
   ```

4. 实现切换（active-class可配置高亮样式）

   ```vue
   <router-link active-class="active" to="/about">About</router-link>
   ```

5. 指定展示位置

   ```vue
   <router-view></router-view>
   ```

#### 2.几个注意点

1. 路由组件通常存放在```pages```文件夹，一般组件通常存放在```components```文件夹。
2. 通过切换，“隐藏”了的路由组件，默认是被销毁掉的(也可以不让他销毁)，需要的时候再去挂载。
3. 每个组件都有自己的```$route```属性，里面存储着自己的路由信息。
4. 整个应用只有一个router，可以通过组件的```$router```属性获取到。

#### 3.多级路由（多级路由）

1. 配置路由规则，使用children配置项：

   ```js
   routes:[
   	{
   		path:'/about',
   		component:About,
   	},
   	{
   		path:'/home',
   		component:Home,
   		children:[ //通过children配置子级路由
   			{
   				path:'news', //此处一定不要写：/news
   				component:News
   			},
   			{
   				path:'message',//此处一定不要写：/message
   				component:Message
   			}
   		]
   	}
   ]
   ```

2. 跳转（要写完整路径）：

   ```vue
   <router-link to="/home/news">News</router-link>
   ```

#### 4.路由的query参数

1. 传递参数

   ```vue
   <li v-for="m in messages" :key="m.id">
    <!--     路由传参方式一：query问号拼接的形式 要用模板字符串`` 和:-->
   <!--      <router-link :to="`/home/message/detail?id=${m.id}&title=${m.title}`">{{ m.title }}</router-link>-->
   
   <!--      方式二对象形式传参相对于第一种 更推荐这种-->
   <router-link :to="{
   	//跳转的地址
   	path:'/home/message/detail',
       //跳转携带的query参数
       query:{
         id:m.id,
         title:m.title,
       }
     }">
      {{ m.title }}
      </router-link>
   </li>
   ```

2. 接收参数：

   ```js
   $route.query.id
   $route.query.title
   ```

#### 5.路由的params参数

1. 配置路由，声明接收params参数

   ```js
   {
   	path:'/home',
   	component:Home,
   	children:[
   		{
   			path:'news',
   			component:News
   		},
   		{
   			component:Message,
   			children:[
   				{
                        //起别名,可以用名字来代替路由
   					name:'xiangqing',
   					path:'detail/:id/:title', //使用占位符声明接收params参数
   					component:Detail
   				}
   			]
   		}
   	]
   }
   ```

2. 传递参数

   ```vue
   <!-- 跳转并携带params参数，to的字符串写法 -->
   <router-link :to="/home/message/detail/666/你好">跳转</router-link>
   				
   <!-- 跳转并携带params参数，to的对象写法 -->
   <router-link 
   	:to="{
   		name:'xiangqing',
   		params:{
   		   id:666,
               title:'你好'
   		}
   	}"
   >跳转</router-link>
   
   <li v-for="m in messages" :key="m.id">
       <!--     路由传参方式一：params要用模板字符串`` 和:-->
       <!--      <router-link :to="`/home/message/detail/${m.id}/${m.title}`">{{ m.title }}</router-link>-->
   
       <!--      方式二对象形式传参-->
       <router-link :to="{
              //跳转的地址,注意如果这里使用params传参,不能使用path只能使用name别名的方式
              name:'xiangqing',
              //跳转携带的query参数
              params:{
                id:m.id,
                title:m.title,
              }
            }">
           {{ m.title }}
       </router-link>
   </li>
   ```

   > 特别注意：路由携带params参数时，若使用to的对象写法，则不能使用path配置项，必须使用name配置！

3. 接收参数：

   ```js
   $route.params.id
   $route.params.title
   ```

#### 6.路由别名

1. 作用：可以简化路由的跳转。

2. 如何使用

   1. 给路由命名：

      ```js
      {
      	path:'/demo',
      	component:Demo,
      	children:[
      		{
      			path:'test',
      			component:Test,
      			children:[
      				{
                            name:'hello' //给路由命名
      					path:'welcome',
      					component:Hello,
      				}
      			]
      		}
      	]
      }
      ```

   2. 简化跳转：

      ```vue
      <!--简化前，需要写完整的路径 -->
      <router-link to="/demo/test/welcome">跳转</router-link>
      
      <!--简化后，直接通过名字跳转 -->
      <router-link :to="{name:'hello'}">跳转</router-link>
      
      <!--简化写法配合传递参数 -->
      <router-link 
      	:to="{
      		name:'hello',
      		query:{
      		   id:666,
                  title:'你好'
      		}
      	}"
      >跳转</router-link>
      ```

#### 7.路由的props配置

> 作用：让路由组件更方便的收到参数

```vue
--------------------------路由配置中-----------------------------------
//起别名,可以用名字来代替路由
name: 'xiangqing',
//:id param方式传参的占位符
path: 'detail',
component: Detail,
//  方式一：路由的props 值只能写死
// props:{a:'111',b:"hello"}
//方式二：值为布尔，会将params传递的参数都当作props传递的值对象进行传递
// props:true
//方式三，函数形式，能够接收query类型
// props($route) {
//     //函数式必须返回一个对象，能够接收到一个$route
//     return {id: $route.query.id, title: $route.query.title}
// }
//方式三的简写形式，插值赋值
props({query}) {
    //函数式必须返回一个对象，能够接收到一个$route
    return {id: query.id, title: query.title}
}
--------------------------路由到的组件中-----------------------------------
export default {
  name: "Detail",
  //接收路由props传递的参数
  // props: ['a', 'b']
  props: ['id', 'title']
}
```

#### 8.```<router-link>```的replace属性

> 当开启了replace模式的时候相当于无痕，每次最新的记录会覆盖上次的

1. 作用：控制路由跳转时操作浏览器历史记录的模式
2. 浏览器的历史记录有两种写入方式：分别为```push```和```replace```，```push```是追加历史记录，```replace```是替换当前记录。路由跳转时候默认为```push```
3. 如何开启```replace```模式：```<router-link replace .......>News</router-link>```

#### 9.编程式路由导航

1. 作用：不借助```<router-link> ```实现路由跳转，让路由跳转更加灵活

2. 具体编码：

   ```js
   //$router的两个API
   //编程式路由使用vc身上的$router实现跳转
   //里面的配置和上方route-link中的to配置一样
   this.$router.push({
   	name:'xiangqing',
   		params:{
   			id:xxx,
   			title:xxx
   		}
   })
   //编程式路由使用vc身上的$router实现跳转
   //里面的配置和上方route-link中的to配置一样
   this.$router.replace({
   	name:'xiangqing',
   		params:{
   			id:xxx,
   			title:xxx
   		}
   })
   this.$router.forward() //前进
   this.$router.back() //后退
   this.$router.go() //前进或后退几步，正数前进，负数后退
   ```

#### 10.缓存路由组件

> 注意：你要缓存的路由要展示在那个组件，就卸载那个组件内(即\<router-view>\</router-view>标签写到那个组件里)

1. 作用：让不展示的路由组件保持挂载，不被销毁。

2. 具体编码：

   ```vue
   <!--keep-alive表示一直活跃，当路由切换的时候不会被销毁-->
   <!--include="News" News表示组件名，如果不写include那么所有在这里用到的组件切换路由时都不会被不会被销毁-->
    <!--缓存多个组件  <keep-alive :include="['组件名1','组件名2', .....]">-->
   <keep-alive include="News">
      <router-view></router-view>
   </keep-alive>
   ```

#### 11.两个新的生命周期钩子

1. 作用：路由组件所独有的两个钩子，用于捕获路由组件的激活状态。
2. 具体名字：
   1. ```activated```路由组件被激活时触发。
   2. ```deactivated```路由组件失活时触发。

#### 12.路由守卫

1. 作用：对路由进行权限控制

2. 分类：全局守卫、独享守卫、组件内守卫

3. 全局守卫:

   ```js
   前置--------------------------
   //全局路由前置守卫---当初始化和每一个路由发生改变之前调用
   //他能接收三个参数，to表示你要去哪里，from表示你从哪里去，next表示放行去下一个的意思
   //vr 是接收到的创建的VueRouter对象
   vr.beforeEach((to, from, next) => {
       console.log(to, from)
       //如果路由中meta的数据有isAuth字段则需要鉴权
       if (to.meta.isAuth) {
           if (localStorage.getItem('school') == 'atguigu') {
               //放行
               next();
           } else {
               alert('学校名不对！')
           }
       } else {
           next();
       }
   })
   
   后置------------------------------------------------
   //全局后置路由守卫，路由成功后执行，他不能收到next
   vr.afterEach((to, from) => {
       console.log("全局后置路由守卫", to, from)
       //路由成功后修改页面的页签
       document.title = to.meta.title || '路由守卫测试'
   })
   //再将vr暴露出去
   export default vr
   ```

4. 独享守卫:

   ```js
   {
       path: 'news',
           component: News,
               //meta是给程序员用的，让程序员定义一些数据
               meta: {title: '新闻', isAuth: true},
                   //独享前置路由守卫，表示只对这个路由起作用
                   //注意没有独享后置路由守卫
                   beforeEnter(to,from,next){
                       console.log(to, from)
                       //如果路由中meta的数据有isAuth字段则需要鉴权
                       if (to.meta.isAuth) {
                           if (localStorage.getItem('school') == 'atguigu') {
                               //放行
                               next();
                           } else {
                               alert('学校名不对！')
                           }
                       } else {
                           next();
                       }
                   }
   }
   ```

5. 组件内守卫：

   ```js
   //组件进入守卫：通过路由规则，进入该组件时被调用
     //不是通过路由规则进入的不会生效
     beforeRouteEnter(to, from, next) {
       console.log("组件内路由守卫-----beforeRouteEnter",to, from)
       //如果路由中meta的数据有isAuth字段则需要鉴权
       console.log(to, from)
       if (to.meta.isAuth) {
         if (localStorage.getItem('school') == 'atguigu') {
           //放行
           next();
         } else {
           alert('学校名不对！')
         }
       } else {
         next();
       }
     },
     //组件离开守卫：通过路由规则，离开该组件时被调用
     //不是通过路由规则进入的不会生效
     beforeRouteLeave(to, from, next) {
       console.log("组件内路由守卫-----beforeRouteLeave",to, from)
       next();
     }
   ```

> **总结路由守卫的顺序：全局前置--->全局后置--->组件进入前--->组件离开之前**

#### 13.路由器的两种工作模式

1. 对于一个url来说，什么是hash值？—— #及其后面的内容就是hash值。

2. hash值不会包含在 HTTP 请求中，即：hash值不会带给服务器。

3. hash模式：

   1. 地址中永远带着#号，不美观 。
   2. 若以后将地址通过第三方手机app分享，若app校验严格，则地址会被标记为不合法。
   3. 兼容性较好。

4. history模式：

   1. 地址干净，美观 。
   2. 兼容性和hash模式相比略差。
   3. 应用部署上线时需要后端人员支持，解决刷新页面服务端404的问题。

   
