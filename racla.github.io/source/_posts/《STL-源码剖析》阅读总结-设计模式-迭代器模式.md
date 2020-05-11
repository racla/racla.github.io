---
title: 《STL 源码剖析》阅读总结 - 设计模式
date: 2020-05-03 14:31:15
tags:
- STL
- C++
categories:
- C++
---
## 简述


关于什么是设计模式及其优缺点的讨论可以在知乎的一些问题上找到：[问题1](https://www.zhihu.com/question/23757237)，[问题2](https://zhuanlan.zhihu.com/p/19835717)。

我的理解是，每一个设计模式都是在程序设计过程中遇到的一些问题的解决方案，这些解决方案在实践中被证明在提升开发效率，代码重用性，可读性等方面都具有一定优势。因此我们理解了每一种设计模式解决的问题后，在编码的过程中遵循设计模式一方面可以帮助我们理清逻辑漏洞，另一方面客观上也提升了代码的质量。[runoob.com](https://www.runoob.com/design-pattern/design-pattern-intro.html)上对于设计模式的使用场景的阐述也比较客观。

本文总结了侯捷《STL源码剖析》一书中提及的STL库中涉及的设计模式。以下引用源码来自[libc++ 10.0.0](https://libcxx.llvm.org/)

## 迭代器模式

### 意图

无需暴露容器内部细节即可访问元素内部元素，因此针对多种容器的同一种操作可以借由迭代器进行泛化，减少了代码量，有利于代码的维护和理解。

例如，下面`includes`函数判断容器中是否包含某个子序列，这个容器可以是`vector`,`list`,`deque`等，对于不同的容器，我们只需要维护下面一份代码就可以了。
```
template <class _Compare, class _InputIterator1, class _InputIterator2>
_LIBCPP_CONSTEXPR_AFTER_CXX17 bool
__includes(_InputIterator1 __first1, _InputIterator1 __last1, _InputIterator2 __first2, _InputIterator2 __last2,
           _Compare __comp)
{
    for (; __first2 != __last2; ++__first1)
    {
        if (__first1 == __last1 || __comp(*__first2, *__first1))
            return false;
        if (!__comp(*__first1, *__first2))
            ++__first2;
    }
    return true;
}

template <class _InputIterator1, class _InputIterator2, class _Compare>
_LIBCPP_NODISCARD_EXT inline
_LIBCPP_INLINE_VISIBILITY _LIBCPP_CONSTEXPR_AFTER_CXX17
bool
includes(_InputIterator1 __first1, _InputIterator1 __last1, _InputIterator2 __first2, _InputIterator2 __last2,
         _Compare __comp)
{
    typedef typename __comp_ref_type<_Compare>::type _Comp_ref;
    return __includes<_Comp_ref>(__first1, __last1, __first2, __last2, __comp);
}
```

### 优点

1. 增加新的容器时不需要修改算法代码

### 缺点

1. 增加新的容器需要添加相应的迭代类，事实上，迭代器设计模式将维护工作从容器的使用者转移到了容器的编写者，相应地减少了使用者犯错的可能。

### STL 实践

首先，迭代器应该是指向容器内部元素指针的包装，我们可以通过该迭代器取出指向位置的值，也可以对迭代器进行算术运算，从而使其指向容器中的上一个元素或者下一个元素。其次，不同容器的迭代器行为是有差异的，例如数组`array`或者`vector`是一块连续内存，因此我们可以让迭代器前进或者后退任意步，操作都是常数时间复杂度；而非连续的容器如双向链表`list`不能在常数时间内跳跃任意步，每次只能前进或者后退一步；单向链表`slist`则只能前进，无法后退。因此，我们应该对每种容器设计相应的迭代器类，而不能只设计一个泛化的迭代器。其三，针对不同类型的迭代器，有时候我们可以利用模板偏特化重载算法。

基于以上几点，STL规定在设计容器的`iterator`类时必须包含指向容器中元素的指针，并且定义相应的前进后退算法,例如双向链表`list`的迭代器设计为：
```
template <class _Tp, class _VoidPtr>
class _LIBCPP_TEMPLATE_VIS __list_iterator
{
    ...
    typedef __list_node_pointer_traits<_Tp, _VoidPtr> _NodeTraits;
    typedef typename _NodeTraits::__link_pointer __link_pointer;

    __link_pointer __ptr_;

public:
    typedef bidirectional_iterator_tag       iterator_category;
    

    __list_iterator& operator++()
    {
#if _LIBCPP_DEBUG_LEVEL >= 2
        _LIBCPP_ASSERT(__get_const_db()->__dereferenceable(this),
                       "Attempted to increment non-incrementable list::iterator");
#endif
        __ptr_ = __ptr_->__next_;
        return *this;
    }

    __list_iterator& operator--()
    {
#if _LIBCPP_DEBUG_LEVEL >= 2
        _LIBCPP_ASSERT(__get_const_db()->__decrementable(this),
                       "Attempted to decrement non-decrementable list::iterator");
#endif
        __ptr_ = __ptr_->__prev_;
        return *this;
    }
    ...
}
```
源码本身比较复杂，需要关注的有三点。一是迭代器内部有一指针`__ptr_`指向`list`容器中的元素`__list_node`，二是`typedef bidirectional_iterator_tag iterator_category;`定义了`__list_iterator`的类型是双向链表，我们可以通过模板元编程利用这一内置类型判断迭代器类型，利用函数重载选择算法的不同版本。三是迭代器定义了运算`++`,`--`，还有`->`,`*`这里没有列出，总之迭代器中应该包含遍历容器中元素需要的算术运算和操作。

容器`list`包含`node`对象指向结尾元素，且可以通过`begin`或者`end`方法获得迭代器：
```
template <class T, class Alloc = allocator<T> >
class list
{
    typedef __list_iterator<value_type, __void_pointer>             iterator;
public:
    __node_base __end_;
    iterator begin() _NOEXCEPT
    {
#if _LIBCPP_DEBUG_LEVEL >= 2
        return iterator(__end_.__next_, this);
#else
        return iterator(__end_.__next_);
#endif
}
```

### 小结

迭代器模式的实现一共有两步：

1. 创建容器专属的迭代器类，包含指针指向容器中元素类型以及指针的算术运算和解引用等操作
2. 创建容器类，包含函数返回指向容器开始或者结束元素的迭代器

迭代器模式是为容器的使用者提供遍历，当需要遍历容器进行操作时，我们首先利用`begin()`方法获得迭代器，如`list.begin()`,然后利用迭代器的`++`和`--`的方法遍历容器，利用`*`或者`->`取指向的元素值。具体例子如文章开始`includes()`函数所示。迭代器也可以看作对指针的包装，如果不使用迭代器我们就需要了解容器的设计细节才能正确使用指针，在容器的设计复杂的情况下(如`deque`)，用户很容易犯错，因此迭代器相当于替用户编写了常用的容器操作。

### 思考
  - 迭代器模式是通过增加间接性对程序进行抽象，从而使得代码的使用者可以在较高的层次上(具体来说就是`iterator`这一接口)对多种类型的容器设计一份或几份（但是少于容器种类）代码，从而减轻了维护代码的负担。
  - 迭代器模式使用范围应该是仅仅限于可以进行迭代的容器，它更通用思想在于上面提到的提取多个类之间的共性，将其作为接口，所有类都实现该接口，从而这些类的使用者无需考虑具体类的细节，利用接口即可实现对这些类的操作，减少了代码量。
  - 设计模式在程序设计初期有用，并且只有我们对需要设计的对象有足够的了解才能知道如何使用设计模式。



