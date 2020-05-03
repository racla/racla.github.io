---
title: Java
date: 2019-09-28 21:11:59
tags:
- Java
- Java基础
categories:
- 中文
- Java
---
# Java基础
## 编译和解释 (待补充)
![Java程序运行过程](https://my-blog-to-use.oss-cn-beijing.aliyuncs.com/Java%20%E7%A8%8B%E5%BA%8F%E8%BF%90%E8%A1%8C%E8%BF%87%E7%A8%8B.png)

C/C++ 编译器将源代码最终编译为机器指令，因此如果要在不同机器上运行程序需要重新编译源码。Java使用另一种方式实现跨平台，即Java虚拟机(JVM)。首先Java编译器(javac, java compiler)将源代码编译为.class文件，当我们需要将程序运行到其他机器上只需要拷贝.class文件运行即可(假设机器都已经安装JVM), JVM会根据具体的CPU指令集架构规范将.class翻译为机器指令。

> 常见的.jar(Java ARchive)是 .class文件的压缩包

## 术语
- JDK, Java Development Kit, Oracla公司针对Solaris, Linux, Windows, macOS等系统发布的Java软件开发包，包括常用的java开发组件：
  - javac: 编译器，将后缀名为.java的源码编译成后缀名为".class"的字节码
  - java: 运行工具，运行.class字节码
  - jar: 打包工具，将相关的类文件打包成一个文件
  - javadoc: 文档生成器，从源码注释中提取文档，注释需匹配规范
  - **jdb debugger: 调试工具**
  - jps: 显示当前java程序运行的进程状态
  - javap: 反编译程序
  - javah: 从Java类生成C头文件和源文件。
  - ***JRE:***  包括一个Java虚拟机（Java Virtual Machine，JVM）以及一些标准的类别函数库（Class Library）
 
 - **Java SE = Standard Edition** 最主要的Java编程平台，包括所有的Java库和API(java.lang, java.io, java.math, java.net, java.util, etc...).
 - **Java EE = Enterprise Edition** 企业版本，添加了容错，分布式等功能
 - **Java ME = Micro Edition** 针对移动设备和嵌入式设备推出的版本，从Java SE中削减了一些功能，并且添加了一些针对移动设备的库
 
 ## JVM 架构
 ![jvm](https://static.javatpoint.com/images/jvm-architecture.png)
 - Class Loader: 类加载器，加载`.class`文件，包括库文件和源码文件，主要由三个内置的加载器构成:
  - Bootstrap Loader 加载`rt.jar`文件，该文件包含了Java SE 中所有的`.class`文件，如java.lang package classes, java.net package classes, java.util package classes, java.io package classes, java.sql package classes.
  - Extension ClassLoader: 加载 `$JAVA_HOME/jre/lib/ext`目录中的扩展文件.
  - System/Application ClassLoader: 加载环境变量`classpath`中的文件，默认为当前工作目录. 可以使用"-cp"或者"-classpath"改变此路径. 
 ```
 public class ClassLoaderExample  
{  
    public static void main(String[] args)  
    {  
        Class c=ClassLoaderExample.class; 
        // 
        System.out.println(c.getClassLoader());
        // String在内置库中，因此有Bootstrap Loader加载，返回null
        System.out.println(String.class.getClassLoader());  
    }  
}
/*
jdk.internal.loader.ClassLoaders$AppClassLoader@1de0aca6
null
*/

- Class(Method) Area   存储运行时常量池，属性，方法数据，方法代码
- Heap 堆，运行时分配的内存
- Stack 栈，
  
 ```
 ## 参考链接
 - [编译模型](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/Java基础知识.md#jvm)
 - [Java Edition](https://stackoverflow.com/a/33908729)
 - [Java 简介](https://www.javatpoint.com/internal-details-of-jvm)

