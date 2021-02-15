---
id: 18
title: RosMockLyn – Mocking an interface
date: 2016-02-04T20:57:34+01:00
author: Qudeid
layout: revision
guid: http://www.alexendris.de/Blog/2016/02/04/7-autosave-v1/
permalink: /2016/02/04/7-autosave-v1/
---
What does one expect from a mocking framework? Well, d’oh… It mocks stuff! But what functionality? How should this functionality be accessed? How does it, once used in a test, read? That all is the topic of this post of my series. Enjoy!

## <u>Past experiences</u>

### RhinoMocks

I’m using mocking frameworks for years now. The first framework I came in touch with was RhinoMocks. While unit tests themselves weren’t new to me, mocking surely was. It was only a year later that I became more interested in mocking frameworks when a new project started at work. So what did I like about RhinoMocks and what didn’t I like?

The interface that you use when creating so called stubs or mocks (I can’t even remember the terminology used) is pretty confusing. You can create a mock, a strict mock, a dynamic mock, a partial mock, stubs… Well see the [documentation](http://www.ayende.com/wiki/%28X%281%29S%28oi2wnukbxzv3tklgolfwaods%29%29/Rhino+Mocks.ashx) of RhinoMocks. That alone is quite confusing. Then there are two ways I know of to do assertions. One is recording the calls and then replaying them – I should note that I’ve never used this part of the API myself! – the other is, apparently the newer one of the two, very similar to other frameworks. Essentially, there is a lot put into RhinoMocks in terms of different APIs, where I personally wonder if that’s helpful or rather limiting.  
The functionality that I liked a lot was the possibility to test if calls were made in a specific order and also the possibility to setup – or stub in RhinoMocks’ terms – one method multiple times with the same or different arguments and return values and you would get the return values in that order as well. 

## 

### Moq

Next in line was Moq. I’ve had a brief look at it in a private project before opting for Moq instead of RhinoMocks. The API is much clearer and less convoluted. Moq goes the way of wrapping the mocked interfaces in their “Mock<>” container. There is one way, and one way only, to create a mock object and that is through the constructor of the aforementioned “Mock<>” class. From then on it’s also pretty straight forward, to setup a method you call “Setup” and at the end you just verify if something has been called though the “Verify” method on the mock object.  
As clear as it is to create a new mock, this way of wrapping the interface has one downside. To pass the actual mock on as an argument you’d always have to call the “Object” property as the mock itself is only a container. To me, this is the biggest downside of this framework and the one thing that is just unnecessarily polluting the test code. Also Moq does not – directly – feature the verification of call order. Only through an add-on this is possible.

<span style="text-decoration: underline;"><span style="font-weight: bold;">Edit:</span></span> I&#8217;ve found that you can also immediately get the mocked object by calling &#8220;Mock.Of<>&#8221; and later on &#8220;Mock.Get(myMock)&#8221; to verify.

### NSubstitute

The latest mocking framework which – I’m also currently using at work – is NSubstitute. And it’s really a merger of RhinoMocks’ API and Moq’s, in my opinion. Order of call verification is there, you don’t use a container for your mocks and the API is very clear in its terminology. Latter is primarily achieved by using very generic extension methods to setup method calls and verifications. I have yet to find significant drawbacks.

## <u>What makes a good mocking framework</u>

Since I don’t want to go into specifics about the decisions I made and subsequently used in my own framework, this is more of a conclusion of the above. Essentially, I like APIs that are very declarative and tell me in one read what this is all about. Let’s see the assertion of NSubstitute as an example:

```csharp
IInterface myMock = Substitute.For&lt;IInterface&gt;();

// do something in your test

myMock.Received(1).DoSomething();
```

And now in comparison to RhinoMocks:

```csharp
IInterface myMock = MockRepository.GeneratePartialMock();

myMock.Expect(x =&gt; x.DoSomething());

// do something in your test

myMock.VerifyAll();
```

When I look at the assertion line for RhinoMocks my first question is, what is ‘all’??? Do I really need to read the entire test to understand what is being asserted? Or differently phrased, shouldn’t the assertion already tell me what is being checked? In my book it should. That’s why I prefer NSubstitute’s API much more than RhinoMocks’.  
I guess to a degree this is personal preference, but considering the fact that we developers should strive to a state where we minimize the time we (need to) read code in quest for understanding what is written, it’s a huge plus.

## <u>Conclusion</u>

Summed up, it is very important to not obscure the test code with distracting elements, be it a container that contains the mock or an assertion that makes me guess and subsequently read the entire test so know what is being asserted. Clean code after all, huh?<img class="wlEmoticon wlEmoticon-openmouthedsmile" style="border-top-style: none; border-bottom-style: none; border-right-style: none; border-left-style: none" alt="Open-mouthed smile" src="http://blog.alexendris.com/image.axd?picture=wlEmoticon-openmouthedsmile.png" />