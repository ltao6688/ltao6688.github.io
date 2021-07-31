---
title: java IO(一) 字节流 Inputstream
author: 方玲涛
top: false
cover: true
toc: true
mathjax: false
date: 2020-08-8 11:33:20
img:
coverImg:
password:
summary:
tags:
- IO流Inputstream
categories:
- java
---

# Java IO 概述分类：

1．java.io 包中阻塞型 IO；

2．java.nio 包中的非阻塞型 IO，通常称为 New IO。这里只考虑到 java.io 包中阻塞型 IO

在 JDK 中1.8 的版本中，该目录下共有 86 个类(或接口)。以下是整个 IO 体系的简单分类：

从数据传输方式或者说是运输方式角度看，可以将 IO 类分为:

- 读写字节：InputStream 和 OutputStream
- 读写字符(Unicode)：Reader 和 Writer

在了解这几个类之前，我们先搞清楚 Java 中的字符与字节的关系。

## Char占几个字节？

之前在 String 源码中分析过，JDK8 中 String 类用的是 char 数组来存储字符值的，到 JDK9 才开始用 byte 数组来存储。这里有一个问题，在 Java 中的 char 究竟占几个字节？毕竟 c++ 中的 char 只占一个字节。对于 char 这个类型，在 Java 中是用来表示字符的，可是世界上有那么多字符，如果有一个char 只用一个字节来表示，那么就意味着只能存储 256 种，显然不合适，而如果有两个字节，那么就可以存储 65536 种。这个数量符合大多数国家的语言字符的个数。于是 Java 团队默认使用 unicode作为编码，一个 char 作为 2 字节来存储。 这里就有两个问题了：

- java的char一定会有两个字节吗?
- 中文字符可以存储在char里面吗?

先来回答第一个问题。char 一定是两个字节吗?不是的，这个跟我们选用的字符编码有关，如果采用 ”ISO-8859-1” 编码，那么一个 char 只会有一个字节。如果采用 ”UTF-8” 或者  “GB2312”、“GBK”  等编码格式呢?这几种编码格式采用的是动态长度的，如果是英文字符，大家都是一个字节。如果是中文，”UTF-8” 是三个字，而  ”GBK”  和  ”GB2312”  是两个字节。而对于  ”unicode”  而言，无论如何都是两个字节。 然后再回答第二个问题，对于一个 char 如果用  ”ISO-8859-1”  来存储的话，肯定无法存储一个中文，而对于 ”UTF-8”、“GB2312”、“GBK”  而言大多数中文字符是可以存储的。

# InputStream

来看一下类的继承图：

![类的继承图](https://raw.githubusercontent.com/ltao6688/blogPic/master/blog/img/20200901222908.png)



事实上，由于 InputStream 是整个继承体系中的顶层类，同时根据数据源的不同，每一种数据源都从InputStream 派生了子类，导致了整个 IO 体系的庞大。但是，虽然整个继承体系很庞大，它还是有迹可循的。做过关于IO操作的读者知道，我们很少单独使用哪个类来实现 IO 操作，平时都是几个类合起来使用，这其实体现了一种装饰器模式 (*Decorator pattern*)。

例如，输入流在默认情况下是不被缓冲区缓存的，也就是说，每次请求读字节时都会使得操作系统再分发一个字节。相比之下，请求一个数据块并将其置于缓冲区会显得更加高效。如果我们想使用缓冲机制，以及用于文件的数据输入流，那么就需要使用下面这种相当恐怖的构造器序列：

```java
DataInputStream din = new DataInputStream(
                            new BufferedInputStream(
                                new FileInputStream("test.txt")));
```

这样确实显得很麻烦，但是却带来了极大的灵活性，健壮优美符合设计模式(~~最啰嗦的语言，没有之一)。不多说了，我们还是继续看源码吧:)

InputStream 类

```java
public abstract int read() 
// 读取数据 具体子类实现

public int read(byte b[]) 
/** 
	将读取到的数据放在 byte 数组中，实际上是调用 read(byte b[], int off, int len)，并将	 偏移量与读取长度设置为 0。在真正被调用的 read(byte[], int, int)中，会调用第一行的抽象 	 read() 方法，该抽象方法会被子类实现。
*/

public int read(byte b[], int off, int len) 
// 从第 off 位置读取 len 长度字节的数据放到 byte 数组中，流是以 -1 来判断是否读取结束的

public long skip(long n) 
// 跳过指定个数的字节不读取，想想看电影跳过片头片尾

public int available() 
// 返回可读的字节数量

public void close() 
// 读取完，关闭流，释放资源

public synchronized void mark(int readlimit) 
// 标记读取位置，下次还可以从这里开始读取，使用前要看当前流是否支持，可以使用 markSupport() 方法判断

public synchronized void reset() 
// 重置读取位置为上次 mark 标记的位置

public boolean markSupported() 
// 判断当前流是否支持标记流，和上面两个方法配套使用
```

## 源码

```java
public abstract class InputStream implements Closeable {
    private static final int SKIP_BUFFER_SIZE = 2048;  //用于skip方法，和skipBuffer相关
    private static byte[] skipBuffer;    // skipBuffer is initialized in skip(long), if needed.
    
    //从输入流中读取下一个字节，
    //正常返回0-255，到达文件的末尾返回-1
    //在流中还有数据，但是没有读到时该方法会阻塞(block)
    //Java IO和New IO的区别就是阻塞流和非阻塞流
    //抽象方法！不同的子类不同的实现！
    public abstract int read() throws IOException;  
    
    //将流中的数据读入放在byte数组的第off个位置先后的len个位置中
    //放回值为放入字节的个数。
    //这个方法在利用抽象方法read，某种意义上简单的Templete模式。
    public int read(byte b[], int off, int len) throws IOException {
        //检查输入是否正常。一般情况下，检查输入是方法设计的第一步
        if (b == null) {    
            throw new NullPointerException();
        } else if (off < 0 || len < 0 || len > b.length - off) {
             throw new IndexOutOfBoundsException();
        } else if (len == 0) {
             return 0;
        }        
        //读取下一个字节
        int c = read();
        //到达文件的末端返回-1
        if (c == -1) {    return -1;   }
        //放回的字节downcast                           
        b[off] = (byte)c;
        //已经读取了一个字节                                                   
        int i = 1;                                                                        
        try {
            //最多读取len个字节，所以要循环len次
            for (; i < len ; i++) {
                //每次循环从流中读取一个字节
                //由于read方法阻塞，
                //所以read(byte[],int,int)也会阻塞
                c = read();
                //到达末尾，理所当然放回-1                                       
                if (c == -1) {            break;           } 
                //读到就放入byte数组中
                b[off + i] = (byte)c;
            }
        } catch (IOException ee) {     }
        return i;
    }

     //利用上面的方法read(byte[] b)
    public int read(byte b[]) throws IOException {
        return read(b, 0, b.length);
     }                          
    //方法内部使用的、表示要跳过的字节数目，
     public long skip(long n) throws IOException {
        long remaining = n;    
        int nr;
        if (skipBuffer == null)
        //初始化一个跳转的缓存
        skipBuffer = new byte[SKIP_BUFFER_SIZE];                   
        //本地化的跳转缓存
        byte[] localSkipBuffer = skipBuffer;          
        //检查输入参数，应该放在方法的开始                            
        if (n <= 0) {    return 0;      }                             
        //一共要跳过n个，每次跳过部分，循环       
        while (remaining > 0) {                                      
            nr = read(localSkipBuffer, 0, (int) Math.min(SKIP_BUFFER_SIZE, remaining));
            //利用上面的read(byte[],int,int)方法尽量读取n个字节  
            //读到流的末端，则返回
            if (nr < 0) {  break;    }
            //没有完全读到需要的，则继续循环
            remaining -= nr;                                       
        }       
        return n - remaining;//返回时要么全部读完，要么因为到达文件末端，读取了部分
    }
    //查询流中还有多少可以读取的字节
    //该方法不会block。在java中抽象类方法的实现一般有以下几种方式: 
    //1.抛出异常(java.util)；2.“弱”实现。像上面这种。子类在必要的时候覆盖它。
    //3.“空”实现。
    public int available() throws IOException {           
        return 0;
    }
    //关闭当前流、同时释放与此流相关的资源
    //关闭当前流、同时释放与此流相关的资源
    public void close() throws IOException {}
    //markSupport可以查询当前流是否支持mark
    public synchronized void mark(int readlimit) {}
    //对mark过的流进行复位。只有当流支持mark时才可以使用此方法。
    public synchronized void reset() throws IOException {

                   throw new IOException("mark/reset not supported");

}
    //查询是否支持mark
    //绝大部分不支持，因此提供默认实现，返回false。子类有需要可以覆盖。
    public boolean markSupported() {           
        return false;
    }
}

```

字节输入流必须提供返回下一个输入字节的 read() 方法。因为所有字节输入流的父类 InputStream 有这样一个抽象方法：public abstract int read()。

[参考文章](https://zhuanlan.zhihu.com/p/65645107)

