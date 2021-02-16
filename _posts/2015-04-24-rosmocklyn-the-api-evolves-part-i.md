---
id: 6
title: 'RosMockLyn – The API evolves: Part I'
date: 2015-04-24T20:07:33+02:00
author: Qudeid
layout: post
guid: /post/RosMockLyn-The-API-evolves-Part-I
permalink: /2015/04/24/rosmocklyn-the-api-evolves-part-i/
categories:
  - .Net
  - Architecture
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
This time around I want to talk about how my API started out.

### Inspiration

Initially, since I was using NSubstitute at work then and still like how its API is. Of course other mocking frameworks I used had their influence as well. Let's start with the creation of mocks.

### Mock creation

Like I wrote in the previous article, the container approach wasn't for me. I just want an instance of the interface. Here, for comparison, other frameworks:

```csharp
// RhinoMocks
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

```csharp
IInterface myMock = Mock.For();
```

Not a hell of a lot different from what NSubstitute does, just a different name, that fits my theme more (Ros**Mock**Lyn, anyone).

### Setup method calls

Looking at other frameworks for setting up methods calls all seem to point into one direction, really:

```csharp
// RhinoMocks
myMock.Stub(x => x.SomeMethod()).Returns(1);

// Moq
mockContainer.Setup(x => x.SomeMethod()).Returns(1);

// NSubstitute
myMock.Setup(x => x.SomeMethod()).Returns(1);
```

Not much to see here apparently. If I remember correctly I initially wanted to deviate from these, doing something like this:

```csharp
myMock.Setup().SomeMethod().Returns(1);
```

I felt this is a much nicer way to do it, but obviously has some drawbacks.

  * If `SomeMethod()` does not return anything the only thing that can follow is a `;`. So all configuration has to go into the “Setup()” call
  * In the end, `Setup()` can't do a lot, but I have to make sure the following call to `SomeMethod()` does something else than normally calling it

So what should happen here?

```csharp
myMock.Setup();
myMock.SomeMethod();
```

There is no way to determine that `SomeMethod()` was **not** called like above. This can be very confusing for the user!

I obviously ended up doing the same as the other frameworks:

```csharp
myMock.Setup(x => x.SomeMethod()).Returns(1);
```

That way I can not only do more than return something after the `Setup()` call, e.g. tell it to throw an exception when called, but also prevent the method from being called as `x => x.SomeMethod()` is treated as an expression. A lot more power for a marginally, if at all, worse API.

The next time I will talk about assertions and argument matching. So stay tuned!