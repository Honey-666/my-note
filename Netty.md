# Netty

> 笔记连接
>
> 1、https://nyimac.gitee.io/2021/04/18/Netty%E5%AD%A6%E4%B9%A0%E4%B9%8BNIO%E5%9F%BA%E7%A1%80/#Netty%E5%AD%A6%E4%B9%A0%E4%B9%8BNIO%E5%9F%BA%E7%A1%80
>
> 2、https://nyimac.gitee.io/2021/04/25/Netty%E5%9F%BA%E7%A1%80/#Netty

## NIO

- non blacking-io  非阻塞IO

### NIO三大组件

#### Channel(双向通道)

- 常见的通道
  - FileChannel           文件数据传输通道
  - DatagramChannel    UDP网络编程传输通道
  - ScoketChannel     TCP数据传输通道(服务端和应用端都可以用)
  - ServerChannel      TCP网络数据传输通道，专门用于服务端

#### Buffer(换成)

- 常见的Buffer
  - BtyeBuffer
    - MapperdByteBuffer
    - DirectByteBuffer
    - HeapByteBuffer
  - ...还有一些Java基本数据类型中的Buffer，不过不常用

#### Selector(选择器)

selector的作用就是配合一个线程管理多个channel，获取这些channel上发生的事件，这些channel工作在非阻塞模式下，不会让线程吊死在一个channel上面，适合连接数多，流量低的场景(流量低是指channel发出的请求数少)

![image-20211107162612039](typora-user-images\image-20211107162612039.png)

### ByteBuffer

> 字节缓冲区
>
> 底层类似于一个数组，有三个指针：
>
> - **capacity**：缓冲区的容量。通过构造函数赋予，一旦设置，无法更改
> - **limit**：缓冲区的界限。位于limit 后的数据不可读写。缓冲区的限制不能为负，并且**不能大于其容量**
> - **position**：**下一个**读写位置的索引（类似PC）。缓冲区的位置不能为负，并且**不能大于limit**

1. 向缓冲区写输入数据调用channel.read(buffer) 方法
2. 调用缓冲区的filp() 方法将写模式改为读模式
3. 调用clear()或compact()将读模式在切换为写模式
   - clear是将position重置位起始位置，compact是将指针放在没有读完的数据后面开始进行写
4. rewind会始终从起始位置读即position的位置为0
5. buffer.mark();   //记录position的位置
6. buffer.reset();    //跳转到mark标记的位置开始

**第一个demo**

```java
@Test
    public void test01() {
        //通过文件输入流获取一个管道对象
        try (FileChannel channel = new FileInputStream("data.txt").getChannel();) {
            //定义一个缓冲区大小为10个字节，注意是nio包下的，他不能通过new来创建对象
            ByteBuffer buffer = ByteBuffer.allocate(10);
            while (true) {
                // 将管道的数据读到缓冲区
                int read = channel.read(buffer);
                //表示这个该文件读取完成
                if (read == -1) {
                    break;
                }
                //将缓冲区改为读模式
                buffer.flip();
                //判断缓冲区是否还有数据
                while (buffer.hasRemaining()) {
                    //一次读一个字节
                    byte b = buffer.get();
                    log.info("char:{}", (char) b);
                }
                //将他在设为写模式，以便后续往缓冲区写数据
                buffer.clear();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

```java
/**
 * 配合使用可以重复读取某一段数据
 */
//记录position的位置
buffer.mark();
//跳转到mark标记的位置开始
buffer.reset();
```

