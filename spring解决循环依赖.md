# spring解决循环依赖

> **一级缓存使用的类型是Map<string,object>**
>
> **二级缓存使用的类型是Map<string,object>**
>
> **三级缓存使用的是Map<string,objectFactory<?>>  三级缓存使用到了动态代理  三级缓存种获取的有可能是普通对象，也有可能是代理对象**

**假设有对象A和B，A对象中有B b属性，B对象中有A a属性**

> **在创建A对象的时候如果发现循环引用，直接进行对象的创建工作**
>
> **先从一级缓存中查一级缓存中没有取三级缓存中查**

1. 首先去一级缓存中查A有没有创建，没有创建A对象，将对象放入三级缓存，这个时候的对象只是实例化完成，并没有初始化，然后创建B对象将对象放入三级缓存，这个时候的对象只是实例化完成，并没有初始化
2. 将A对象放入二级缓存，移除三级缓存中创建的对象，这个时候对象只是实例化完成，没有初始化
3. 将A对象给到B对象中的A a属性，进行赋值，这时B实例化完成，初始化完成，在三级缓存中
4. 将B放入一级缓存 实例化完成，初始化完成  删除二级缓存中的B，删除一级缓存中的B
5. 往A里面设置B b 的属性，A实例化完成，初始化完成，在二级缓存中
6. 将A添加到一级缓存，移除三级缓存，移除二级缓存，这时A，B全部实例化完成，初始化完成，**至此A对象创建完成**

> **B对象的创建**

1. 创建B，先从一级缓存中取，一级缓存中有，将B返回

![image-20210410160705536](typora-user-images\image-20210410160705536.png)