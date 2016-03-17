---
layout: post
title: "Empathy Driven Programming - Documentation"
date:  "2016-03-08 00:00 UTC"
tags: Documentation
---

##Background##

A while a go, I gave this lightning talk about "Programming for a less
intelligent person". I don't think that talk went very well and the reason was that I couldn't
truely convey what I meant by that and the title was a bit mean. However, this topic has been on my
mind for a long time and I finally found a better way to describe what I mean
by it.

You see, I have been a programmer of some years and I've been coding in
Ruby for more than five years now but I have mostly worked on private code
bases that belonged to a company and not opensource.
I have been the person who maintains, refactors and cleans up other people's
good and bad design decisions. The person who quietly sends you love for
creating simple, readable code or curses for creating unnecessary complications for a simple problem.

One thing you should know about me though is I have an average IQ and an average knowledge of
programming and design patterns. Regardless of this person being a literal or conceptual example of
myself, this is most likely the person who is going to read and maintain
your code and not yourself. unless of course, you sign a contract that lets the employer kill your puppy(or
kitten) if you resign!

So, I started thinking about the consept of programming for someone
else. Somebody who is figuratively less intelligent than you are. Now,
this does not mean that the next person is actually less intelligent or
less knowledgable, it only means that you try to come up with simple
designs and be very descriptive when you write a piece of code.

My plan is to create a series of blog posts that focus on each aspects
of coding with empathy which means coding with care about others who are
going to read, learn, maintain or use our code.
Documentation is the first in this series. I hope you enjoy it and help
me spread care and empathy to this amazing comunity and even further.

#Documentation#

Everybody hates documentation, right? it's time consuming, easily
becomes out of date and it does not have a real value.
To be honest with you, I agree with this statement in some
circumstances. The truth is, documentation is only a way of
communication and does not bring any obvious values unless you know
exactly what they are!
Let's review together what problems it can solve and how.

##The problem##

I once asked myself why is it that opensource libraries have
more or less a set of maintained documentation whereas organisational
code bases don't usually have any. The answer is pretty obvious if you
think about it: "Unlike organisation employees, opensource contributers don't have the luxury of
face-to-face communication with their users and other contributers."
If you create a new opensource library, you must be online all the time
and you must be describing the same things over and over again for every
user of your library and everybody who wants to help you with it. So,
basically you have no choice but to have at least some sort of Readme file,
Example or a public interface documentation to communicate what your
library is doing and how to interact with it. You also need to regularly maintain it so it does not go out of date with new versions.

We often forget about this in private sectors. what usually happens is that
somebody decides to build a startup. the first step would be to create
an MVP (Minimal Viable Product) in the least time you can get. this
often means really bad and quick design decisions as well as no
documentaions. And it works because at that point we don't really care
about communication or even design decisions! we only need to sell our idea at that stage.
But after the startup is grown and company hires more new developers and loose old ones, we start thinking about refactoring and
improving parts of the codebase and that's where documentation comes in handy. We need some sort of communication with our future selves as well as our present and future colleagues.

Here is a list of situations and problems that really beg for proper documentation:

1- When your organisation have a big source codes with losts of local gems and lots of local apps
2- When you often need to find and ask others what a piece of code is doing or where a peice of functionality lives in the codebase
3- When you sometimes find a peice of code that nobody knows what it is really doing because the owner is no longer with the company
4- When you use a piece of code that was meant to be deprecated without you knowing about it
5- When you solve the same problem that your colleagues have solved before. Note that, you may or may not find out about this!
6- When you find debugging production difficult due to different versions of local gems and apps

Proper documentation can definitely make a big improvement in all those situations.

##How can documentation help me##

- production debugging issue: shows all docs and source codes related to
  gems and apps that are currently in production. easier to debug prod
issues.

Note: address all the problems above and how this can solve each one.

##How can i leverage this##

- first step: installing and running yard on all local gems and apps.
  only this step, already helps with prod issues, etc.
- creating a standard way of documenting the code that suits best for
  the organisation
- teaching team members the standardized way
- Be flexible with the way you document and let your team be a bit
  creative but ask them to let others know about it too
- documenting the existing code. start with pain points and observe how
  it affects team communcations, performance, etc.
- this is a good opportunity to audit parts of your code that require
  some love. if you find duplications, create refactoring tasks for
them.
- assign someone to be the champion of this. find a volunteer who has a
  passion for organising and cleaning up the code
- add standard documentation to your code review process. be picky about
  the words and vocabulary that is used in the docs. they should be
clear and easy to undrestand and even short. i would go as far as
putting a limit on how many words that can be used on each doc.

##Conclusion##

even if we don't start documenting since day one, a big source code and
developer frustrations is a good sign for the need to make this change.
make small changes and observe how it affects the team.
