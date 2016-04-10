---
layout: post
title: "Documentation"
subtitle: "Code with care!"
date:  "2016-04-08 00:00 UTC"
tags: Documentation
---

This is the second post in the series of "Code with care" posts.
In the first one which you can find [here](http://sjahandideh.github.io/2016/03/08/edd.html), I gave a thorough explanation of EDD and why I intended to write about it. In this post, I focus on Documentation which is by nature one of the products of Empathy Driven Development.

## Why Documentation?

Everybody hates documentation, right? it's time consuming, easily becomes out of date and it does not add a tangible business value. To be honest with you, I agree with this statement in some circumstances. The truth is, documentation is only a way of communication and does not bring any obvious values unless you know exactly what they are!
Let's review together what problems it can solve and when and how we can leverage documentation.

### When should I add documentation?

I once asked myself why is it that open source libraries have more or less a set of maintained documentation whereas proprietary code bases don't usually have any. It turned out that the answer is pretty obvious: "Unlike organization employees, open source contributers don't have the luxury of face-to-face communication with their users and other contributers." If you create a new open source library, you must be online at all times and you must be describing the same things over and over again for every user of your library and everybody who wants to help you with it. So, basically you have no choice but to have at least some sort of Readme file, example or a public interface documentation in order to communicate what your library is doing and how to interact with it. You also need to regularly maintain that documentation so that it does not go out of date with new versions of the code.

We often forget about this simple fact in private codebases. what sometimes happens is that somebody decides to build a startup. the first step would be to create an MVP (Minimal Viable Product) in the least time they can get. this often means hasty design decisions as well as no documentations. And it works because at that point we don't really care about communication or even design decisions! We only need to sell our idea at that stage. However, after the startup is grown and the company starts hiring new developers and loosing old ones, we start thinking about refactoring and improving parts of the codebase and that's where documentation comes in really handy. We do need some sort of communication with our future selves as well as our present and future colleagues.

Here is a list of example situations that really beg for proper documentation:

  1. Your organization has a big source code with many local gems (libraries) and apps
  2. You often need to ask others what a piece of code is doing or where a piece of functionality lives in the codebase
  3. You sometimes find pieces of code that nobody knows what they are really doing because the owner is no longer with the company
  4. You are using a part of the code that was meant to be deprecated without you knowing about it
  5. You solve the same problem that your colleagues have solved before. Note that, you may or may not find out about this!
  6. You find debugging production code difficult due to different versions of local gems and apps
  7. Your company is in need of hiring somebody only to answer questions of others

### How can documentation help and how to get the best out of it

If you or your company have any of the mentioned problems, then proper documentation can make a big improvement to your code-base. Let's look at each one to see how can documentation help.

#### 1. Your organization has a big source code

Imagine your big source code with updated, descriptive documentation. Imagine every class, module and public method has a full description of what they are intended for and good examples of how they can be used. Imagine how much time it would save you if you were trying to understand how and when a method is called. The truth is we can't always enforce the correct design pattern in every single situation, but with descriptive examples the reader can at least get a quicker view of what the writer had in mind when they wrote or updated that piece of the code. For one second, put your bias away and honestly answer this simple question of whether proper documentation help understanding a big, complicated source code or not.

#### 2. You often need to ask others

The main reason to ask questions is to clear up things that we don't understand. If the people who wrote a piece of code that you are trying to understand added some good examples and notes about what it was doing and how it could be used, you wouldn't need to find them to clear up the current behavior because it was already described for you.

#### 3. You sometimes find pieces of code that nobody knows what they are really doing

By writing proper descriptions, we are explaining what we were thinking at the time and what brought us to make those decisions. This will reduce misunderstandings. I think this is a very important point because firstly, if you care about your image as a programmer, you care about having your name of a piece of code as well. By explaining in writing, you are leaving that legacy for someone else. And for that matter, proper documentation brings a sort of respect and value to the writer of the code.

#### 4. You are using a part of the code that was meant to be deprecated

As our codebases grow, we often find ourselves in situations where there's nobody or a very few number of people who are familiar with every little part of the code. this means that if we decide to change things or add new things, we can't always be sure that it doesn't exist already. Sometimes we try to remove an old ugly piece but it's so entangled into the system that it seems quite dangerous. the best way would be to add new, clean pieces to the code without touching the old bits to be removed later. How would you let your fellow colleagues about them? If you have proper documentation that everyone reads, maintains and values, you can add tags like **"@deprecated for [new feature]"** to let others know they shouldn't be using this anymore. later, when people haven't used the deprecated code for enough time, you can go back and remove it as parts of your code cleanup process.

#### 5. You solve the same problem that your colleagues have solved before

If your documentation tool provides easy searching, you can search by names of the classes and methods and even by custom tags that you create. Not only this will decrease your search time, but it also helps reducing the possibility of creating unnecessary duplications. Now I know that your editor provides a really good searching tool but having a web-based search that displays descriptions nice and clean, visually helps much more. Compare finding things directly inside [Rails codebase](https://github.com/rails/rails) to finding things within [Rails Rdoc](http://api.rubyonrails.org/).

#### 6. You find debugging production code difficult

Imagine you have apps A, B and C that use your local gem called G. each of the apps may refer to different versions of G. Now imagine trying to solve a production bug related to gem G on app B.

The hard way to find the problem is:

1. check the release version of app B
2. find out what version of G it's using. to do this, you should either ssh to production box or checkout that version locally or go to github.
3. checkout that version of gem G locally or look it up in production or github to find the problem

"How can documentation help in this case?" I'm glad you asked. If you have a place, for example an AWS S3 or a local server that gets updated
to the release version of your documentations for each app and it's dependencies, this is what you need to do:
1. go to [docs.yourcompany/app-B/gem-G]

There you go! you can see the descriptions, examples and also the source code of the gem G for the release version.

#### 7. Your company is in need of hiring somebody only to answer questions of others

If your company has a big sourcecode and many developers, it needs a place or a person to be the source of truth. Now, I'm not denying that having such roles is not useful but how can you guaranty that those people will always remain in their roles? And don't you think investing in hiring and educating people for that role is more expensive and time-consuming and error-prune to maintain a proper documentation system for your codebase?

### The price to be paid

We talked about the benefits of having documentation. Let's now talk about the price that you have to pay to make it work properly.
Maintaining decent documentation is more than just a technical decision. It's a cultural change and requires commitment from everyone in the team. Your team must:

1. write documents for new code
2. update documents with every change
3. review documents as well as the code
4. find a way to upload documents that reflect production codebase

But these are not the only things. You need to make sure that your team is just writing documentation to get it over with. People should really think about how they can help that other less-intelligent person by writing that description, otherwise it will turn to be just a chunk of unnecessary clutter in your code. The real value of documentation is its expressiveness.

Also, since this is a cultural change, you should make sure that your team goes all the way. Don't just mention it to them! set it up, practice it, teach it, create and maintain the culture for it and in one sentence, **Fully execute it!**

### How to execute this in my organization

- Install and run a documentation tool such as (yard)[http://yardoc.org/] on all local gems and apps. This step alone can already help with some of the mentioned issues.
- Create a standard way of documenting the code that suits best for the organization. You can pick a source code that you respect and follow their style of documentation, for example.
- Teach your team members how to document their code and follow the style-guide
- Be flexible with the way you document and let your team be a bit creative about it. Ask them to let others know about it too
- Add documentation checks to your code review process. Be picky about the words and vocabulary that is used in the docs. They should be clear and easy to understand and even short. I would go as far as putting a limit on how many words that can be used on each doc.
- Devise a plan to document the existing code. Start with the pain points and observe how it affects team communications and performance. Note that this is a good opportunity to audit parts of your code that require some love. If you find duplications, create refactoring tasks for them.
- Assign someone to be the owner of this. Find a volunteer who has a passion for organising and cleaning up the code. This way you can guaranty that proper documentation is always on the table.

## Conclusion

EDD is all about acknowledging that third person. By thoroughly and properly documenting your code base, you are creating a permanent communication framework with your future self, your teammates and in general, with that imaginary less-intelligent third person who will read and maintain your code. You are explaining to them why you made that decision at the time and how to use it. You are putting yourself in their shoes. Well done :)

**Let's all Code with care!**
