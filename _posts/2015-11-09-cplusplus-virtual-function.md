---
layout: post
title: "C++虚析构函数"
date: 2015-11-09 18:12:20 +08:00
tags: c++
---

为什么C++ 的父类的析构函数需要加**virtual**关键字？

我们先来看一段实例代码:

{% highlight cpp %}
class Object
{
public:
    Object()
    {
        cout << "Object() called" << endl;
    }

    virtual ~Object()
    {
        cout << "~Object() called" << endl;
    }
};

class InheritObject : public Object
{
public:
    InheritObject()
    {
        cout << "InheritObject() called" << endl;
    }

    ~InheritObject()
    {
        cout << "~InheritObject() called" << endl;
    }
};
{% endhighlight %}

上述代码是一个简单的继承关系，我们下面做一个测试。

{% highlight cpp %}
int main()
{
    {
        InheritObject ihtObj;
    }

    return 0;
}
{% endhighlight %}

编译运行，查看输出结果为：

> Object() called  
> InheritObject() called  
> ~InheritObject() called  
> ~Object() called

这个很容易理解，内存使用的常用方法，先new的后释放，保证不会造成空指针调用。这里解释了子类与父类的构造函数和析构函数调用顺序的问题。

我们在来看另一个测试。

{% highlight cpp %}
int main()
{
    Object *obj = new InheritObject();
    delete obj;

    return 0;
}
{% endhighlight %}

编译运行，这次的结果为：

> Object() called  
> InheritObject() called  
> ~InheritObject() called  
> ~Object() called

和上面的输出相同。但是在这次测试中，我们使用父类指针接受了一个子类的对象。那么在delete的时候，我们看到的是父类的指针，怎么才能将子类的内存释放呢？这里就有一个c++的多态机制了，在运行时去决定调用哪个函数，这里就用到了virtual函数的作用了。

综上所述，析构函数上加**virtual**关键字，是为了保证在多态调用中，不会导致内存泄漏。
