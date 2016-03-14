---
layout: post
title: "Empathy Driven Programming - Documentation"
date:  "2016-03-08 00:00 UTC"
tags: Documentation
---

A while a go, I gave this lightning talk about "Programming for a stupid
person".
yea, I can create abbriviations too!
I don't think that talk went very well and the reason was I couldn't
truely convey what I meant by that. However, this topic has been on my
mind for a long time and I finally found a way to describe what I mean
by it.

You see, I have been a programmer of some years and I've been coding in
Ruby for more than five years now but I mostly worked on private code
bases that belonged to a company and not opensource. And I'm also not a quick fixer kind of person, so I don't enjoy working as a contractor for a short time in a new-born
startup either. That leaves me to be the person who maintains, refactors
and cleans up other people's good and bad design decisions. The person
who quietly sends you love for creating simple, readable code or curses
for creating unnecessary complications for a simple problem.
One thing you should know about me though is that I may not be as smart as
you are! I'm a person whith average IQ and average knowledge of
programming and design patterns.
Regardless of this person being a literal or conceptual example of
myself, this is most likely the person who is going to read and maintain
your code unless you sign a contract that let them kill your puppy(or
kitten) if you resign!

So, ....  I invented SDP. "What does that stand for?" you ask.
Well, SDP stands for "Stupidity Driven Programming". Now, I'm pretty
sure there are many nicer hipster alternatives to that phrase but since I'm a non-native english speaker, we stick with it! I invented it and I call it whatever I want, so just shut your brain up
and keep reading.

##The problem##

- big source codes, losts of local gems, lots of local apps
- private repos, not opensource. but the same problem still exists.
- should ask a lot of people what they really meant by sth at the time.
  if i can still find that person of course
- deprecated things get lost and never really get deprecated. people
  could still use methods, services that meant to be deprecated.
- different people solve same problems over and over again because it's not clear that somebody else has solved it before.
- deployed code can use different versions of each local gem. very
  difficult to debug problems that happen in production.

##How can this help me##

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
