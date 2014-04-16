---
layout: post
title: "Interface re-implementation in C#"
description: ""
category: 
tags: [C#]
---
{% include JB/setup %}

Yesterday I came across the concept of interface re-implementation in C#. It is possible to restate an interface on a class that already inherits that interface from its base class or another interface. For example, say you have the following classes and interfaces:

{% highlight c# %}
interface IBaby
{
  void Cry();
}

class Baby : IBaby
{
  public void Cry()
  {
    Console.WriteLine("Cry cry!");
  }
}

class CryBaby : Baby
{
  public new void Cry()
  {
    Console.WriteLine("CRY CRY CRY CRY!!");
  }
}
{% endhighlight %}

It is obvious that CryBaby implements IBaby as it derives from Baby. However, the 'Cry' method of the interface is bound to the implementation in Baby, as the implementation in CryBaby does not override but hide the one in Baby. So when you run the following code snippet, the output will be 'Cry cry!'.

{% highlight c# %}
IBaby cryBaby = new CryBaby();
cryBaby.Cry();
{% endhighlight %}

Now say that we redeclare the class CryBaby as follows. The body of the class stays the same, but we explicitly state that we implement the IBaby interface on the class:

{% highlight c# %}
class CryBaby : Baby, IBaby
{
  [...]
}
{% endhighlight %}

Now the output of the previous code snippet will be 'CRY CRY CRY CRY!!'. This is caused by the fact that the compiler will recalculate the the mapping between interface and class methods (a.k.a. interface mapping) again when a class explicitly restates that it implements an interface. In this case this means that the 'Cry' method of the interface is bound to the new 'Cry' method declared in CryBaby, instead of to the original 'Cry' method in Baby.

Before I ran into this concept I always assumed that restating an interface on a class was, at best, duplication, as the class already implemented the interface through a base class or interface. I use Visual Studio with ReSharper, and ReSharper always indicates when an interface stated on a class is superfluous and can be removed. I just tested the above code and indeed ReSharper says the IBaby interface is not superfluous on the CryBaby class as it changes the interface mapping.

While investigating I came across [this post by Eric Lippert](http://blogs.msdn.com/b/ericlippert/archive/2011/12/08/so-many-interfaces-part-two.aspx) that describes a possible pitfall of interface re-implementation. He also does a good job of explaining the concept in [this post](http://blogs.msdn.com/b/ericlippert/archive/2011/04/04/so-many-interfaces.aspx).
