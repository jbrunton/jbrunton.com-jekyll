---
layout: post
title: Principles of Good Software Design
tags:
    - Software Design
    - Software Complexity
    - Incidental Complexity
---

It's a well understood precept that well written software should avoid unnecessary complexity - but formally defining the kinds of complexity we seek to avoid is difficult, so how should we set about recognizing code which is in need of simplification?

## (Trying) To Define Complexity

There are plenty of definitions for complexity to choose from.  A quick Google reveals quite a number:

...formal definitions...

But how useful are these definitions?



## Incidental Complexity

There's another way we can categorize complexity, however.  Wikipedia also highlights the distinction between incidental (or accidental) and inherent (or essential) complexity:

> Accidental complexity: Relates to difficulties a programmer faces due to the chosen software engineering tools. A better fitting set of tools or a more high-level programming language may reduce it.
Essential complexity: Is caused by the characteristics of the problem to be solved and cannot be reduced.

In my opinion, this isn't general enough.  Plenty of code features incidental complexity - i.e. that which is not inherent to solving the problem at hand.

In a blog post, Peter Rosser describes a related kind of complexity:

> System complexity is a property of a system that is directly proportional to the difficulty one has in comprehending the system at the level and detail necessary to make changes to the system without introducing instability or functional regressions.

> <cite>[Peter Rosser](http://blogs.msdn.com/b/peterrosser/archive/2006/06/02/softwarecomplexity.aspx)</cite>

Here we're getting somewhere.  What's most critical to having a system which one can reason about, develop and maintain is to avoid local complexity.  If you can safely make local modifications, then it matters less how complex the whole application is - you don't need to understand it all at once to develop.

I prefer a more succinct, definition, however:

> Incidental complexity is anything which distracts from solving *the problem at hand*.

I prefer this formulation because it highlights (even without the emphasis) the multiplicity we should be concerned with: at any one point in our code, we should be solving for one problem.  One of the most

incidental complexity:
- incorrect choice of algorithm/solution/data structure
- complexity incidental to solution of problem (i.e. solving multiple problems simultaneously)
- too abstract

so Simplest def: that which distracts from the problem at hand.  Thus both solving multiple problems, and solving with extra abstraction (introducing extra levels of indirection), are incidental. (Also consider abstraction in terms of problems with eg visitor)

avoid incidental complexity, and the others will follow?
- solving problems individually => much easier to avoid complex branching after decomposing problem (so reduces cyclomatic complexity of individual functions)
- 

* intro - what is complexity, and why avoid it?  intuitively, ....
* various formalizations
* incidental vs accidental (and definitions)

