---
id: 27
title: 'RosMockLyn – The API evolves: Part I'
date: 2021-01-25T15:32:25+01:00
author: Qudeid
layout: revision
guid: http://alexendris.com/2021/01/25/6-revision-v1/
permalink: /2021/01/25/6-revision-v1/
---
This time around I want to talk about how my API started out.

### &nbsp;

### Inspiration

Initially, since I was using NSubstitute at work then and still like how its API is. Of course other mocking frameworks I used had their influence as well. Let’s start with the creation of mocks.

### Mock creation

Like I wrote in the previous article, the container approach wasn’t for me. I just want an instance of the interface. Here, for comparison, other frameworks:

<pre class="EnlighterJSRAW">// RhinoMocks
MockRepository repo = new MockRepository();
IInterface myMock = repo.GenerateStub();

// Or
IInterface myMock = MockRepository.GenerateStub();

// Moq
Mock mockContainer = new Mock();
IInterfae myMock = mockContainer.Object;

// NSubstitute
IInterface myMock = Substitute.For();
```

Obviously, NSubstitute is the cleanest and easiest to read. It clearly state what is happening without any fuzz around it. And that is what I wanted as well. Et voilà:

<pre class="EnlighterJSRAW">IInterface myMock = Mock.For();
```

Not a hell of a lot different from what NSubstitute does, just a different name, that fits my theme more (Ros**Mock**Lyn, anyone<img class="wlEmoticon wlEmoticon-winkingsmile" style="border-style: none;" src="http://blog.alexendris.com/image.axd?picture=wlEmoticon-winkingsmile_1.png" alt="Winking smile" /> ).

### Setup method calls

Looking at other frameworks for setting up methods calls all seem to point into one direction, really:

<pre class="EnlighterJSRAW">// RhinoMocks
myMock.Stub(x =&gt; x.SomeMethod()).Returns(1);

// Moq
mockContainer.Setup(x =&gt; x.SomeMethod()).Returns(1);

// NSubstitute
myMock.Setup(x =&gt; x.SomeMethod()).Returns(1);
```

Not much to see here apparently. If I remember correctly I initially wanted to deviate from these, doing something like this:

<pre class="bEnlighterJSRAW">myMock.Setup().SomeMethod().Returns(1);
```

I felt this is a much nicer way to do it, but obviously has some drawbacks.

  * If “SomeMethod()” does not return anything the only thing that can follow is a “;”. So all configuration has to go into the “Setup()” call
  * In the end, “Setup()” can’t do a lot,&nbsp; but I have to make sure the following call to “SomeMethod()” does something else than normally calling it

So what should happen here?

<pre class="EnlighterJSRAW">myMock.Setup();
myMock.SomeMethod();
```

There is no way to determine that “SomeMethod()” was **not** called like above. This can be very confusing for the user!

I obviously ended up doing the same as the other frameworks:

<pre class="EnlighterJSRAW">myMock.Setup(x =&gt; x.SomeMethod()).Returns(1);
```

That way I can not only do more than return something after the “Setup()” call, e.g. tell it to throw an exception when called, but also prevent the method from being called as “x => x.SomeMethod()” is treated as an expression. A lot more power for a marginally, if at all, worse API.

The next time I will talk about assertions and argument matching. So stay tuned!