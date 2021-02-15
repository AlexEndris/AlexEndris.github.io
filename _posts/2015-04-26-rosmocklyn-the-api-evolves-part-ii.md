---
id: 5
title: 'RosMockLyn – The API evolves: Part II'
date: 2015-04-26T20:37:10+02:00
author: Qudeid
layout: default
guid: /post/RosMockLyn-The-API-evolves-Part-II
permalink: /2015/04/26/rosmocklyn-the-api-evolves-part-ii/
categories:
  - .Net
  - Architecture
  - Clean Code
  - Mocking
  - RosMockLyn
  - Testing
tags:
  - .Net
  - API
  - Architecture
  - 'C#'
  - Design
  - Framework
  - KISS
  - Mock
  - Mocking
  - Moq
  - NSubstitute
  - Readability
  - RhinoMocks
  - Setup
---
Last time I wrote about the creation of mocks and how I solved setting up method calls. Let’s delve into assertions.

### Assertions

What is a mocking framework without the ability to check whether something on the mock was called. Again for comparison:

<pre class="EnlighterJSRAW" data-enlighter-language="generic">// RhinoMocks 
myMock.Expect(x =&gt; x.SomeMethod());
// the test code 
myMock.VerifyAll();
// or 
myMock.AssertWasCalled(x =&gt; x.SomeMethod());
// Moq 
myMock.Verify(x =&gt; x.SomeMethod());
// NSubstitute 
myMock.Received().SomeMethod();```

&nbsp;

Of course there is not much those differ in, except maybe the first shown here from RhinoMocks. Same as with setting up methods I wanted to do the same as NSubstitute does here but ran into the same problems as before. I’ve actually never checked if NSubstitute has similar issues that I discovered. Anyhow, I do it like so:

<pre class="EnlighterJSRAW">myMock.Received(x =&gt; x.SomeMethod()).AtLeastOne();
```

The assertion is done in that last part. So leaving that out actually does nothing at all apart from blowing some CPU cycles into the air.

### &nbsp;

### Argument matching

My personal favourite! It took a while until I found a good way to do this, although in the end I was very inspired by one of the above frameworks, can’t remember which, though. First of all, API wise none of the mocking frameworks I mentioned differ much. Maybe another name but roughly the same functionality.  
So, what do I mean by ‘argument matching’?

For instance, I might want to check if a method of my mock was called but I don’t care about with what parameters, or I don’t care about one of the parameters. That’s where argument matching comes in:

<pre class="EnlighterJSRAW">myMock.Setup(x =&gt; x.SomeMethod(Arg.Any()));

myMock.Received(x =&gt; x.SomeMethod(Arg.IsIn(1,2,3,4))).One();
```

Those two are just examples, there are more of them.

I really wanted to start explaining how this is done, especially since how can an integer tell it should be ‘any’? Long story short – I might explain it in more detail in a separate post – there is some re-routing logic on the mock statically.