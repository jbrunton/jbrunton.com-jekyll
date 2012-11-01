---
layout: post
title: Principles of Good Software Design
tags:
    - Software Design
    - Software Testability
    - Software Complexity
---

## The goals of good software design

To reiterate what I've said elsewhere on this site, there are real benefits to having well designed code: good code (i.e. that which is readable, maintainable and testable) is cheaper to develop, safer to change, and hiring and on-boarding new engineers becomes much easier.

Let's quickly outline what we have in mind when we talk about these qualities of a codebase:

* **Readability**: If I look at a unit of code, can I easily understand it?
* **Maintainability**: If I modify a unit of code, will all dependent units of code change their behavior consistently?  Will I have to make further changes across unrelated pieces of code, because the first unit of code is not authoritative in the system?  Can I easily refactor and generalize code, or is it too tightly coupled?
* **Testability**: Can I quickly write effective unit and feature tests?

Taking these as our three overarching goals for good software design, how do we achieve these aims?  What are the principles that underpin all well engineered applications?  What are the signs we should be looking for that might indicate our codebase is below par, and how should we address them?

## The basic principles

I find it most useful to start with two foundational principles from which all our goals (and related tenets of software engineering) follow.  These are:

1. Keep your code **DRY**.
2. Avoid **incidental complexity**.

Briefly (I will elaborate on all these points below): clarity of code follows from avoiding incidental complexity; DRY code is inherently maintainable.  And in order to achieve these two properties, we must first structure our application so that it is loosely coupled and strongly cohesive; we must ensure we solve programming problems using patterns and paradigms appropriate for the problem domain; and we must avoid black-box "God classes" and methods by decomposing complex units of code with a high cyclomatic complexity.  Finally, once we have achieved these properties, we find that we've achieved testability too.

Here's a visual diagram (the goals are in red, the design principles are in blue, and an arrow from one item to another indicates that the first requires the second to hold):

<a target="_blank" href="/public/images/principles-of-software-design.png">
<img class="prettyimg" alt="Principles of SOftware Design" src="/public/images/principles-of-software-design.png" />
</a>

Now to examine these principles in detail.

## 1. Keep your code DRY

The basic idea behind what Benjamin C. Pierce calls the *Abstraction Principle* is well understood by most developers:

> Each significant piece of functionality in a program should be implemented in just one place in the source code. Where similar functions are carried out by distinct pieces of code, it is generally beneficial to combine them into one by *abstracting out* the varying parts.

> <cite>Benjamin C. Pierce, *[Types And Programming Languages](http://www.cis.upenn.edu/~bcpierce/tapl/)*, MIT Press</cite>

Avoiding duplication is at the heart of writing maintainable software.  But the DRY principle has greater scope than even that:

> DRY says that every piece of system knowledge should have one authoritative, unambiguous representation. Every piece of knowledge in the development of something should have a single representation. A system's knowledge is far broader than just its code. It refers to database schemas, test plans, the build system, even documentation.

> <cite>*[Orthogonality and the DRY Principle](http://www.artima.com/intv/dry.html)*, a conversation with Andy Hunt and Dave Thomas</cite>

Most systems I've worked with are far from completely DRY in this regard: knowledge is often redefined across the data schema, data model, and frequently across various user interfaces too.  However, accepting that there are degrees of DRYness, and that developing *completely* DRY code often requires a not insignificant investment in code generator tools (or other means to translate knowledge across tiers in an n-tier application), the benefits of at least some degree of abstraction are clear.  Wikipedia puts it well:

> When the DRY principle is applied successfully, a modification of any single element of a system does not require a change in other logically unrelated elements. Additionally, elements that are logically related all change predictably and uniformly, and are thus kept in sync.

### Coupling

Loose coupling is a property most developers will aspire for, but let's take the time to clarify what we mean by this and why it's desirable:

> Coupling is the degree to which each program module relies on each one of the other modules.

> <cite>Wikipedia entry on *Coupling*</cite>

Now, consider the effect of a high degree of coupling on our application.  As soon as we introduce a high level of coupling between modules, the difficulty of managing dependencies starts to impede our ability to abstract out commonalities.  Thus, loose coupling is what enables abstraction or DRY code: it is a requisite of maintaining a DRY codebase.

## 2. Avoid incidental complexity

In an earlier post on [software complexity](/blog/2012/10/04/avoid-incidental-complexity/) I examined why *incidental complexity* is a useful intuitive notion of the kind of software complexity we should avoid, and I gave my own definition for how I think about it:

> Incidental complexity is anything which distracts from solving *the problem at hand*.

I'm currently of the opinion that there are three distinct causes of incidental complexity:

1. The application lacks cohesion: modules aren't grouped according to function, which makes it hard to find and understand external dependencies of a unit of code.  To put it another way: modules which aren't cohesive have a higher incidental complexity, because they solve a variety of unrelated problems.

2. The programming problem at hand isn't being solved with the most appropriate "phrasing" (i.e. the choices of data structures, algorithms or language paradigm may have been poor; or the limitations of the language itself may be giving rise to incidental complexity).

3. Multiple problems are being solved at once (in a single class or method) &mdash; i.e. subproblems of the problem are being solved in the same unit of code, adding significant background noise.

Any of these three will add obfuscation to the code, making it harder to reason about; they should all be avoided.

## Testability

Once we've achieved code which satisfies all of the above, we should find our code is immediately testable:

* We've decomposed solutions to distinct problems into distinct units of code.  Thus, we should be able to keep our tests orthogonal, we should be able to achieve high test coverage, and we should be able to clearly define expected outcomes on the individual units of code.
* We've reduced dependencies through a loosely coupled architecture, so it should be straightforward to use test doubles in our tests.

## A virtuous circle

A happy outcome of following the above principles is that each one positively reinforces the others.  For example:

* Writing tests will often highlight flaws in code (such as tight coupling) which would impede abstraction.
* Decomposing problems (to reduce incidental complexity) improves testability of the code by providing distinct, named interfaces to test against; and it will facilitate abstraction, as having distinct, named interfaces simplifies identification of the individual authoritative representations of system knowledge.

The principles are thus mutually reinforcing.
