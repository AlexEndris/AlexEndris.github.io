---
id: 8
title: RosMockLyn – An introduction
date: 2015-04-09T22:47:34+02:00
author: Qudeid
layout: default
guid: /post/RosMockLyn-An-introduction
permalink: /2015/04/09/rosmocklyn-an-introduction/
categories:
  - .Net
  - Mocking
  - Roslyn
  - RosMockLyn
  - Testing
tags:
  - .Net
  - 'C#'
  - Compile-time
  - Mocking
  - Roslyn
  - RosMockLyn
  - Testing
  - Unit Testing
---
Now that I’ve been working on this project for quite some time, I was thinking it was about time to at least write about it. It’s going to be a story about how I had the idea and why I needed to go for it. Here goes!

# 

## <u>From the beginning</u>

Playing the online game League of Legends for quite some time, I was very happy when the developer Riot Games enabled anyone to access their newly created REST API to facilitate third party applications to use data from them. This data includes a match history, statistics about certain games, who is currently playing with who etc. The curious one I am, I started a small Windows Phone application that should get this data, save it to a database and show some neat statistics. One thing started to bother me really quick, though. None of the mocking frameworks available in .Net would work on that platform. The reasoning is pretty easy. Microsoft prohibits run-time code generation and execution and thus things like Castle.DynamicProxy, which is used by many mocking frameworks, like RhinoMocks, Moq and NSubstitute. The MSDN documentation about testing Windows Phone apps also tells us to implement mocks by hand.

For me, this was unacceptable. I didn’t want to constantly implement mocks. So I was looking for compile-time mock creation frameworks and actually found one. The only one: [MoqaLate](https://moqalate.codeplex.com/). I played a bit with is, but found it not at all convenient enough. I as a user of such a mocking framework don’t want to bother with anything at all. I just want to mock my interfaces. How I get those mocks, I don’t care. Be it run-time creation, compile-time creation… Apart from this, as already apparent on the start page on Codeplex, MoqaLate isn’t really maintained anymore and still lacks a lot of features. Especially parsing of code is done manually which leads to things like spaces that break everything.

No, I wanted… needed something better, something more convenient, something I don’t have to care about other than just use it. I also didn’t want to do the parsing myself, I don’t want to write my own C# compiler. \*drum role\*

## <u>Project Roslyn meets mocking</u>

I believe the name of this post already gave it away, didn’t it? I don’t even know if this is going to be the final name of the mocking framework, but compile-time mocking, CT-Mock… I don’t know, just wasn’t catchy enough for me. I guess RosMockLyn does it, for the time being. This mocking framework is also going to be of the compile-time sort. It’s a necessity for Windows Store/Universal Apps because of the severely limited reflection support, as mentioned above. I won’t tell you everything just yet, though.<img class="wlEmoticon wlEmoticon-winkingsmile" style="border-top-style: none; border-bottom-style: none; border-right-style: none; border-left-style: none" alt="Winking smile" src="http://blog.alexendris.com/image.axd?picture=wlEmoticon-winkingsmile.png" /> With this post I’m actually starting a series about this project. Things I considered, design choices, challenges, you name it.