---
title: 《STL 源码剖析》阅读总结 - 设计模式 适配器模式
date: 2020-05-03 20:59:48
tags:
---

## 适配器模式

### 意图
通过新增的适配器类将我们设计的类转化为客户需要的类。
### 使用场景
程序需要使用的类与现有的类之间不同，通过Adapter类进行二者之间的转换。具体如数据库访问时的底层核心适配器，STL只允许读的iteration
### 实现方法
继承或依赖（包含）
### STL 实践
STL中适配器模式主要在三个地方有体现：
- 应用于容器：如 queue和stack都是对 deque的包装，二者包含了一个deque类型的成员，二者的接口都是转而调用deque的接口。
- 应用于迭代器：如reverse_iterator, insert_iterator, istream_iterator,ostream_iterator等，后两个类的设计比较优雅
- 应用于函子：我认为这是适配器模式最普遍和最重要的应用了，它将一个类适配成一个函数，实现方法是通过向类中添加一个`operator()`函数，这样的类被称为函子(functor)。例如，一个可以比较两个数大小的函子：
```
template<class T>
class less{
public:
    bool operator() (const T & a,const  T & b)
    {
        return a < b; // 该操作需要类型T重载了全局操作符 <
    }
}
/*
在使用时，我们调用表达式：
less<int>()(1,3)
即可得到一个bool值
*/
```
进一步地，我们可以利用该函子的返回值作为另一个函子的输入，从而形成链式结构：
```
class not{
public:
    bool operator()(bool x)
    {
        return !x;
    }
}
/*
可以使用以下链式结构调用:
not()(less<int>()(1,3))
匿名对象的写法不直观，我们将其改写为具名对象：
less<int> less;
not not;
not(less(1,3)); // 此时类not 和less的行为与函数一般无二，另外这两个对象还可以被当作参数传递，从而替代了函数指针的功能（在不深究二者之间效率的情况下）
*/
```

### 思考
1. 迭代器模式是程序库设计者为兼容不同版本的库（如java1.1 Enumeration 和java 1.2 Iteration）或者同一功能的不同的增强实现(如C# SqlConnection和MySqlConnection)而提出的解决方案，如无必要不要使用该模式。
2. functor的实现是否是其他语言如Java，C#中函数作为参数传递的方法？
3. 函数对象的实现依赖于C++可以实现重载运算符`()`
