---
layout: post
title:  Plato's Cave - Attending iDesign Master Classes
description: Learn the basics of SSL and HTTPS and understand why and how to configure your web server outside of IIS using HTTP.sys
date:   2015-07-25 00:00:00
categories: Thoughts Software Architecture
author: Yoad Snapir
summary: My experience of taking the iDesign master classes. Warning, contains some troubling truths.
tags:
  - iDesign
  - Software Architrecture
  - PDMC
  - AMC
  - Project Design
comments: true
---

##Learning Hurts##

> "The more I learn, the more I realize [how much] I don’t know." A. Einstein

I recently realized how I can tell the difference between a normal learning experience and a great one. Any learning experience plugs a hole in your knowledge fabric, a great one violently expands that fabric leaving much more holes to be filled. When I have a great learning experience I partly wish I had not learned it at all - It is that violent and that great.  
I had two recent experiences like that, both were [IDesign][idesign] classes, both were given by [Juval Löwy][lowy].


##The Elusive Software Architect##

Being a software developer by profession and a problem solver by passion, I grew up into doing what I do today. Things mostly came naturally - that was, apparently, a big mistake.

Somewhere along my career path people expected of me to tell other developers what to do. Hacking on my own keyboard no longer made the impact they had in mind. And what is the best way of spreading the efficiency and capabilities of a good software engineer? Promoting him to be a developer++ role of course. Right? (Wrong).  
The basic problem was that I came to believe I was now a "Software Architect" / "Team Leader" / "Tech Lead" without having a clue what that meant. All I knew is that I was good at something I loved, and I should get others to love and do it the same way.  

Admittedly, I want to believe I made a difference nonetheless. But deep inside I still knew something was wrong. Hacking at keyboards was never enough, and resorting to hacking on other develoeprs' keyboards ~~if~~ when the project took a turn for the worse simply did not scale. On top of that, I could never answer in confidence simple questions like "When will this project be delivered?", "How much people do we need?", "What are the risks?"

I had a formal education in software engineering, and an informal education from doing software for large organizations and still - I had no concept of what a successful software project looks like.

Looking for answers, I also attempted fixing "The architecture" of the software I designed. This was a step in the right direction, even if only it got me to the IDesign class "The Architect's Master Class" (AMC).

Turns out the problem was not (only) in the architecture, but rather in my understanding of how software is created and the crucial role of the Software Architect in that. Juval spends the better part of the class charting out the fallacies of the software industry and links that back to a lack of engineering discipline throughout.

Let me summarize it freely, most of us are lazy, trusting too much in the hands of the unskilled, and willingly blinding ourselves to the fact that building software is a multi facet activity, with many unknowns and risks which without proper planning will sink any project down - regardless of personal skill and effort. In fact, Whatever we feel is intuitive about software projects is plain wrong (e.g. Throwing more people at it would not make it meet the deadline).

The accidental reason I took the AMC was the presense of the word "Architect" in its name. The reason you should take it is to learn the truth about yourself and the profession you are in. Unless, you would not want to face the pain in realizing that truth.

##The IKEA allegory##

Juval mentions IKEA furniture once in a while, would you buy such a furniture without its assembly manual? Do you imagine creating such assembly manual regardless of the design of the furniture itself? So many things could go wrong if you separate the two. Think impossibly complex furniture no one could ever build, or a construction process leaving out a large chunk of the furniture.

As an Architect, when designing a system, you must always design the plan to execute the construction of that system. Who else should?  
Who else would know the right order things should be integrated?  
Who else is able to lead a good effort estimation of the plan?  
Who else could calculate the risk in executing such a plan and abort it in time? (i.e. before it is attempted)

That duality, the design of the system and the design of the construction plan for that system is what differentiates an engineering effort from a gamble. It applies to software systems, and any other complex system for that matter. Juval brings many examples from other more established engineering fields to drive a "Well, of course!" feeling that is hard to shake off.

Attending the next class was an even deeper disruption to what I believed was true.

##Measure Thrice, Check Twice and Cut Once##

The "Project Design Master Class" (PDMC) is an unremarkable name for a class that hides in my opinion an even larger boon than the AMC does. So unremarkable in fact, that before, during and after taking the class I could not explain in a sentence to anyone who asked what it was about without a feeling I am leaving something out. I usually mumbled something like "It's about planning, and projects, but for software, but not only, and in a surprising way..." but usually they dosed off and lost interest anyway after they heard the word "planning".

If you read up until here I would take my chances with more than a sentence.

The class reveals the great chasm between what the common software engineers think about when they hear "Software Project Plan" and the actual thing which should take place. A great effort, logic and discipline go into **designing** the project which would materialize the system that was designed.

Again, in short, designing a project requires evaluating different design options, their costs, durations, complexity, and risks, learning the nature of the project and gaining an instinctive *feel* for it. The options are presented and one is approved and followed by the team. The insights and understanding gained during the design process are a prime tool navigating the sea of uncertainty awaiting any execution of a complex plan. Reacting to problems as soon as possible, *projecting* where the effort is headed and resorting to plan B (or C or D or E) when in need.

These efforts payoff even when the design process clearly indicates the project is a dead-end (not enough funds, or not enough time, or too risky). Without that, you just head out to the unknown, wasting everybody's time, money and will.

Perhaps all the above is natural to you, it sure wasn't for me. But the level of attention to details, the sheer confidence and reliance on good engineering when designing the project is an eye-opener, no less. Juval mitigates real concerns with real tools. Down to the finer points of how best to present the plans to the decision makers allowing them to choose only good options. (or actually preventing them from making bad ones)

##And a Show It Is##

Juval is known to appreciate the scholars of old. He is relying and borrowing from other fields and aspects of life to better understand and explain our industry. While standing on the shoulders of giants is good, imagine the benefits of a giant standing on these shoulders. This post is not about Juval, but for lack of a better place I would like to give my personal point of view on the person behind IDesign.

Juval is sharp, and using his own terms, he "get's it". His analytic skills combined with a remarkable ability of deduction is key for the effort he and the IDesign team are making in educating for better practices in the software industry. We are surrounded with good and bad examples of engineering in all aspects of life - ignoring that is madness, learning from that is a bliss.

Attending the class, on top of what I said before, was a great show. Juval is a top presenter, and clearly enjoys what he is doing.

##LEGO and Entropy##

Since I was a young child I remember always comparing processes to LEGO. Specifically to either building a large dome from many LEGO bricks or smashing that same dome back to the pile of bricks it was created from.  This was my intuitive way of understanding entropy, and my practical way of telling the difference between applying force to organize something (against the universe) and applying force to disorganize something (with the universe). Things that go with the universe would go there anyway eventually. The mind of a child, go figure (pun intended).

![Obi-Wan Lego Minifigure]({{ site.baseurl }}/assets/images/attending_idesign_classes/obi_lego.jpg)

I understand that building software goes against the universe, and as such, it is a process you need to apply force to. That force translates to the construction and the design of the construction. I understand that as well, but still not sure I am up to the task.  
As I told someone I appreciate lately, once I will be standing on the other end of a successfully run software project, I will know. Up until then, it's all theory, and it's on me to prove it.

##What about You?##

If you also sense something is wrong, gambling your way in and out of projects, could not fully grok the affects of the efforts you or your team are making, I urge you to go and take one of the classes. Either one would be enough to give you a glimpse of where the true problems are. The rest will be up to you, the same way it is up to me now.

[idesign]: http://www.idesign.net/
[lowy]: http://www.idesign.net/About#Lowy