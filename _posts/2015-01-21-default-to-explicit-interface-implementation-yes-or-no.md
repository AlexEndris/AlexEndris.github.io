---
id: 11
title: Default to explicit interface implementation – yes or no?
date: 2015-01-21T19:44:00+01:00
author: Qudeid
layout: default
guid: /post/Default-to-explicit-interface-implementation-yes-or-no
permalink: /2015/01/21/default-to-explicit-interface-implementation-yes-or-no/
categories:
  - .Net
  - Architecture
  - Clean Code
tags:
  - .Net
  - Architecture
  - 'C#'
  - Explicit implementation
  - Inheritance
  - Interface
---
I recently started at a new company. One of the first things that struck me as odd was the extensiveness of interfaces being implemented explicitly in one of the projects. I soon found out that one particular colleague made it his default to always implement interfaces explicitly.  
Interested as to why he was defaulting to this he had a few arguments: 

  * Hiding the methods when using the concrete implementation

I seemed many of my new colleagues weren't so adapt with the usage of an IoC-Container and were still new up classes.

  * Refactoring the interface

If some methods are not needed anymore and are removed from the interface, there will be complaints from the compiler.

Gotten very interested in this topic, I made an effort of browsing the web to find out who else does this and what arguments they had. I, myself, was very skeptical of this interface implementation default. Not soon after starting my quest for the truth about the question whether it is good or bad I found this post on code project:

[http://www.codeproject.com/Articles/392516/Why-I-use-explicit-interface-implementation-as-a-d](http://www.codeproject.com/Articles/392516/Why-I-use-explicit-interface-implementation-as-a-d "http://www.codeproject.com/Articles/392516/Why-I-use-explicit-interface-implementation-as-a-d")

Reuven Bass essentially listed the very same arguments plus one that endorses the fact that explicitly implemented members of a class can't be accessed even by the class that implements them themselves.

This is where my search ended, the skepticism, however, didn't really fade away. Let me have some words on the aforementioned arguments.

# <u>Discussing the arguments</u>

When my colleague told me that people would just new up classes even though StructureMap was in place and people were told to use it, I wondered if it really is such a great idea trying to educate people this way. Let me put something straight. If I wanted to new up a class that implements an interface explicitly, I can still do it. I can even use all the implemented methods. Of course, I would have to do some ugly casting for it to work, but essentially there is nothing to stop me from doing something like this. 

```csharp
interface IBall
{
    void Bounce();    
}

class Ball : IBall
{
    void IBall.Bounce()
    {
        // some nifty bounce logic...
    }
}

void Main()
{
    IBall ball1 = new Ball();
    Ball ball2 = new Ball();    

    ball1.Bounce(); 
    ((IBall)ball2).Bounce(); // compiles and works perfectly well!
}
```

While I absolutely agree that casting like this isn't the way to go, I also think that the reason behind this argument isn't as valid as it might seem. Wouldn't it be so much better if people weren't only told what to do but also explained why using an IoC-Container and working more with interfaces, especially in a huge code base, is a good way to go? We don't want developers that do something because someone told them it's great, but because they also know the reason why! Also, having a separate interface for every tiny class might also not make sense, but how are they to know whether something makes sense or not, if they don't even know why they do something in the first place?

While I'm already on the topic of casting, I'd like to tackle Reuven's final argument. Let's have a look at this snippet implementing the same interface as above:

```csharp
class Ball : IBall
{
    void IBall.Bounce()
    {
        // the bounce got better
    }
    
    private void LetItBounce()
    {
        Bounce(); // Poor compiler 
    }
}
```

Let's ignore for a second that this code doesn't make any sense and nobody is really able to call the `LetItBounce` method. This will result in a compiler error anyhow. I, again, agree with the general idea behind this argument. We want to minimize the dependency of implementation details on the implementation of the interface. But again, it just feels wrong and doesn't solve anything. We can, like proclaimed, use casting again. This would compile and work perfectly:

```csharp
class Ball : IBall
{
    void IBall.Bounce()
    {
        // bounce it baby!
    }

    private void CastTheBall()
    {
        ((IBall)this).Bounce();
    }
}
```

Okay, now it's gotten really nasty. But what can you do? Some people just prefer the path of least resistance. And yet again this is easily avoidable by communication, helping people to understand and especially enabling them to not see things as black and white, because they aren't. There is also another way to do the same thing:

```csharp
class Ball : IBall
{
    void IBall.Bounce()
    {
        ThePerfectBounce();
    }

    private void ThePerfectBounce()
    {
        // our incredible bounce logic
    }

    private void LetItBounceAgain()
    {
        // some other stuff
        ThePerfectBounce();
    }
}
```

Now I want to ask… What is the benefit of the explicit interface implementation now? We want the code reuse, because somewhere else in our class there is a need for the same code and it absolutely makes sense to extract it into one place to be accessible for others to consume without duplicating the logic. In a case like above I wonder why it should be prohibited or made a chore to reuse the code of the implementation. We want to encourage code reuse, don't we? If only part of the implementation is needed elsewhere, it's needless to say that this should be shared as well. But my point here is, that I can't really see the benefit of the explicit interface implementation for this argument.

Last, but not least, refactoring. Assuming we don't have much tooling beside our IDE for refactoring etc. it is certainly helpful if the compiler tells us that something isn't needed anymore. But as soon as we factor in some of the tools out there (like ReSharper) this point loses some weight as well. For instance, ReSharper can tell us that the previously implemented method isn't used anywhere anymore. But, Alex, if you don't look into the class that implements the interface, you won't see the hint ReSharper puts there! If that's what you thought, you are right. It might well be that the method will just sit there, orphaned, without any future, abandoned… so sad. But enough with the sentimentalism! Refactoring code should never be done lightly. What if our Bounce() method is the only caller of other methods? Well, our dear compiler has certainly abandoned us now. It won't bicker around that this one method was only used in our implementation of Bounce(). So in the end it doesn't really help us cleaning things up, because at the end of the day we still have to look into all classes that implement our interface, because for one we still have to remove the method and second have a look at the rest of the class in order to find methods that now aren't called anymore. Yes, we get a list of all the implementations. That's good, at least. But does it really justify explicit implementation, especially considering my counter arguments above? It seems like a trade not worth taking.

I still got another one up my sleeve, though. Inheritance. What if we have this scenario:

```csharp
class Ball : IBall
{
    void IBall.Bounce()
    {
        // back to a normal bounce
    }
}

class SuperBall : Ball
{
    // ...?
}
```

Now… that's annoying. I want to have a SuperBall that can bounce even higher than our dear normal Ball… but I just can't get it to bounce! Well… explicit interface implementation breaks inheritance. There is no way that we can change the implementation of Bounce() like this. We have to find a workaround! How about this?

```csharp
class Ball : IBall
{
    void IBall.Bounce()
    {
        BounceImpl();
    }
    
    protected virtual void BounceImpl()
    {
        // Bouncy
    }
}

class SuperBall : Ball
{
    protected override void BounceImpl()
    {
        // Damn! That's what I call a bounce!
    }
}
```

Sweet! Now we can change how the implementation of our bounce. But wait… what is that? Someone changed how bounce is implemented in Ball…

```csharp
class Ball : IBall
{
    void IBall.Bounce()
    {
        BounceImpl();
        // some other bouncy code...
    }
}```

That's really mean. Here we were rejoicing, that our SuperBall was out-bouncing any other ball and this guy just changed something. Obviously, when calling IBall.Bounce() the other bouncy code is going to be executed no matter what we do in our SuperBall. We cannot elect to skip this code. Okay, so this solution doesn't really work too well… How about we to it like this?

```csharp
class SuperBall : Ball, IBall
{
    void IBall.Bounce()
    {
        // Yaaay, super bouncy!
    }
}```

Yes, this actually works, believe it or not. With doing it like this, we hide the Bounce() implementation in Ball and if we call IBall.Bounce() – where IBall is actually a SuperBall – we really call the implementation in SuperBall. You can take my word for it. Unsurprisingly, I have a problem with this, too. There is no way to tunnel down the inheritance. Imagine this was a methods that persists something. I would've wanted to extend Ball and only have to worry about the new things introduced by SuperBall to persist. I can't call “base.Bounce()”. Not even with casting. In this case I'd, again, have to go back to our previous scenario with all its implications.

# <u>The bottom line</u>

Despite what you might think now. My answer to the question in the title is “it depends”. It's what I am trying to convey especially in my earlier counter arguments. It is neither good nor bad to use explicit interface implementation outside of its original reason of existence (please refer to the code project article above, Reuven also explained that). It is important, though, to know exactly why or why not to elect to use this technique in a certain scenario. Consider the implications of your choice. It is also fine to use it to try to force other people to actually use the interface rather than the concrete class. But, please, as a last measure. We are all software developers because we love the rapid train that is changing technology and, thus, the opportunity to learn so many new and exciting things every day. Make a difference and explain your colleague of your ways of using IoC and interfaces over being “old school”.