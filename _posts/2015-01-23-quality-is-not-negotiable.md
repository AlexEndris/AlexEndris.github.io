---
id: 10
title: Quality is not negotiable…!
date: 2015-01-23T21:03:40+01:00
author: Qudeid
layout: default
guid: /post/Quality-is-not-negotiable…!
permalink: /2015/01/23/quality-is-not-negotiable/
categories:
  - Architecture
  - Product Management
  - Scrum
tags:
  - extremes
  - KISS
  - product owner
  - SOLID
  - YAGNI
---
Sounds great, doesn’t it? Let me guess, you already worked on a project, that just had really nasty code, where you wondered if those that created it really knew what were doing. I guess we’ve all been there. On both, the receiving side, meaning having to maintain such a code base, but also on the other side, when we just started programming. I’ve recently heard this statement more and more. But you might already expect it, I’m not going to sell myself completely to the statement above. I will highlight two viewpoints, put them together in an imaginary scenario and then draw a conclusion.

# 

## <u>The developer</u>

Alright, alright. I admit it. I like it when the codebase is nicely structured, well thought out and just a pleasure to look at. To phrase it as a user story: I as a developer would like to have my code extensible, testable and SOLID. I mean, let’s face it, programming is an art and artists strife for perfection.

## <u>The product owner</u>

If you are not familiar with Scrum, basically the product owner is the voice of the customer towards the development team. The product owner decides what is going to be implemented, while maintaining and conveying the vision of the product towards the developers. Consequently, he thinks about the value things that are done have for the customer. He is, thus, by definition, the opposing force. The product owner wants his features fast. He doesn’t care if the code is an art piece, it just has to work the way it should.

## 

# 

## <u>Building a story</u>

Now, what does this mean in a broader sense when put together? We have a little discrepancy here, the product owner wants his product as fast as possible on the market, which is inhibited by the developers who design their code perfectly so that they are ready for every little thing that is about to come. In short, art takes time, perfection takes time, and you really have to make sure every little bit is covered with perfection. That is certainly not as fast as the product owner would like it to be. At the same time, just mashing the colours onto he canvas results in a that big mess of a code base that I mentioned at the beginning of the post. I believe it is pretty obvious where I’m aiming at, but please bear with me.

Let’s imagine a project. In the beginning, everything was done as fast as possible, not caring about quality at all, just pushing out feature after feature. Over the years the mess got worse and worse. There a little bit of technical debt, another workaround here and there. Years later the code is barely maintainable, there is very little test coverage, if at all and new features are hell on earth to weave into all the tightly coupled code. It can’t continue like this and at some point someone high up decides, from now on we have learnt from our mistake of “just doing” and we won’t ever go back there. Quality is not negotiable! While there is still the main project, smaller side projects arise and, since we don’t want to get into the mess again, we apply all we can find, domain driven design here, some IoC there.

Thinking about it, it doesn’t sound too bad, but the user stories start to take longer to implement. But since we don’t want to commit the mistakes of the past – and everybody is constantly reminded of them when working on our monolithic code – nobody is really going to notice, because now we’re doing the right thing! Up until the point of over-engineering, because everybody is now so focused on putting all the techniques into the projects that the big picture might get blurred.

## <u>The conclusion</u>

In the scenario I painted, we resulted in two extremes. One being the a focus on time to market, while the other one is a focus on code quality. As very often in life, extremes are bad. One makes the code unmaintainable, the other creates a lot of flexibility and overhead that might in the slightest be needed. There might be the time where a very new idea the world has never seen is about the be implemented, but since there is so much focus to make the code just perfect it might already be to late and someone else had the idea but implemented it much faster. Rush to market lost. Also, things that take longer to finish subsequently cost much more and while still in development there is little to no return of investment.

It’s the same thing with children being raised with pure authority. They experienced it as really bad and swear to themselves that when they got kids, they will never do this. In the end it results in no raising at all, the child can just do what ever it wants, because authority was perceived as bad. It is very easy to fall from one extreme to the other, one is bad the other is good. Black and white. But what about the shades in between?

In the end it comes down to adapting to the situation. If something has to be out as fast as possible, hack it! But don’t just keep hacking in the code without thinking afterwards. Take the code that might be a bit ugly and refactor it to not be restrained by bad software architecture. At the same time, if there is not too much time pressure take the bit of time to create well structured code. But not too much, why would I implement another OR mapper or construct everything with five decorators when it is not leading to anything valuable? Great, I can easily extend every piece of code there is without a problem, but 80% of that will never be extended, but time used to code it like this is gone. There is not that one way that always works. It might be easier to just say “Quality is not negotiable”, shut off the brain and create a piece of art. Rather walk the shades of grey than jumping or lingering in one of the extremes. Keep KISS (keep it simple, stupid) and YAGNI (you aren’t going to need it) in mind as well. Know when it makes sense to take technical debt and see it as an investment that can still be straightened out.