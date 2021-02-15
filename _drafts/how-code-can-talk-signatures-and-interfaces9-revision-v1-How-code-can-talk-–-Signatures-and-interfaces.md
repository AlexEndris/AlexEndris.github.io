---
id: 20
title: How code can talk – Signatures and interfaces
date: 2016-02-04T20:58:59+01:00
author: Qudeid
layout: revision
guid: http://www.alexendris.de/Blog/2016/02/04/9-revision-v1/
permalink: /2016/02/04/9-revision-v1/
---
I’ll start off with a small code sample. What could be read out of the signature of this method alone:

```csharp
void DoSomething(IList&lt;MyClass> list)
```

I intentionally chose a non-talking method name, because I want to elaborate on the parameters here, especially. So, “DoSomething” expects a parameter that implements the IList<T> interface. Which enables the writer of this method to change the content of the list. Add, remove, insert, clear items. Let’s reveal what this method actually does:

```csharp
void DoSomething(IList&lt;MyClass> list)
{
    foreach (MyClass item in list)
        Console.WriteLine(item.Name);
}
```

&nbsp;Huh, interesting. Turns out I was wrong. No item is added, removed or inserted, nor is the list being cleared in there. But why is an IList<MyClass> needed then? I tell you: It isn’t! From what is done inside the method an IEnumerable<MyClass> would suffice and mind there isn’t even a cast needed, as _IList<T> is a superset of IEnumerable<T>, meaning IList<T> does the same, just more. Why is that relevant now, you might ask? Essentially, for the thing that happened above. I expected the method to use the IList interface for a reason. In case this was a method in some third party library – and I’m going to ignore the fact that in theory we can disassemble a .Net library – we would either have to rely on the documentation, test what happens or just expect something to happen that the name of the method didn’t give away (side effects). Wouldn’t it be much better to see an IEnumerable<MyClass> there? I mean, it wouldn’t hurt the implementation, yet anyone who uses this method knows exactly that our list isn’t changed in any way – on a side note, though: any item in the list can itself change, if not immutable, but no item can be substituted by another or even removed from the list._

## <span style="text-decoration: underline;">Custom interfaces</span>

Of course it is the same with custom interfaces. Let’s say we have a class called “Repository” that implements the “IRepository” interface which, in turn, derives from “IReadOnlyRepository”. While the repository itself can store, update and get items, the read-only repository can only get items. Same as above applies: There is a lot being communicated if an IReadOnlyRepository is requested over an IRepository. We know, rather than assume, that data is only retrieved.

## <span style="text-decoration: underline;">Casting</span>

Don’t do this at home: 

```csharp
void DoSomething(IEnumerable&lt;MyClass> list)
{
    IList&lt;MyClass> realList = (IList&lt;MyClass>)list;

    realList.Add(new MyClass());
}
```

&nbsp;While this compiles perfectly fine, this might result in runtime exceptions since we, the ones who wrote this method, can’t expect people to hand us something that is actually an IList, while we only tell them that we want an IEnumerable. Again, like above, this is about the communication through code. We tell the consumers of our method, that we will only iterate over the list provided, we don’t care if it is just an array or a list or even a dictionary. We simply cannot assume an IList is given us. Also the .Net 3.5 newly introduced “.ToList<T>()” extension method doesn’t help here. “ToList” will always create a new list. So changes made will not be in our “list” variable.

## <span style="text-decoration: underline;">Conclusion</span>

Mainly for public interfaces – and all public methods of any class can be considered an interface unless hidden in another way, like having the class internal rather than public – it is very important to communicate your intentions. Don’t you want to be sure if you use this one method that expects a list, this list isn’t altered? Put yourself in the consumer’s mind. What would you think if you didn’t know the implementation but wanted to use it? At the same time, if you really need an IList, make sure any side effects – things that alter the list or how the list could be altered – are communicated in some other way. Be it comments, easily accessible documentations or, most preferably, the method name!