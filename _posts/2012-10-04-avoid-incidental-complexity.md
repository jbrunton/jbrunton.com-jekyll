---
layout: post
title: Avoid Incidental Complexity
tags:
    - Software Design
    - Software Complexity
    - Incidental Complexity
---

It's a well understood precept that well written software should avoid unnecessary complexity &mdash; but formally defining the kinds of complexity we seek to avoid is difficult. How, then, should we set about identifying code which is overly complex (i.e. what are the tell-tale signs); and how can we improve it?

## Defining Complexity

There are plenty of definitions for complexity to choose from.  Here's one which seems intuitive:

> Complexity in software is perceived as the number of interacting elements within a software system, the internal structure of these elements, and the number and nature of interdependencies among them

> <cite>R. Taylor, N. Medvidovic and E. Dashofy,  *Software Architecture: Foundations, Theory and Practice*, Hoboken, NJ: John Wiley & Sons, 2009.</cite>

There are also plenty of theoretical metrics available to help us measure the complexity of our programs.  A couple of the more popular ones:

* [Cyclomatic complexity](http://en.wikipedia.org/wiki/Cyclomatic_complexity) is a measure of the number of execution paths through a program.
* [Halstead metrics](http://en.wikipedia.org/wiki/Halstead_complexity_measures) give figure for the *length* and *vocabulary* of a program (and, although originally conceived for procedural programs, can easily be [adapted for OOP languages](http://www.virtualmachinery.com/sidebar2.htm)).

But how useful are these definitions in practice?  Is it really useful to put a figure on the complexity of your application?  It's probably clear that your multi-platform web service (complete with mobile apps and SMS notifications) has a higher degree of complexity than the rake task which deploys it &mdash; but how does that help you make informed decisions about the structure of the application?  Is the level of complexity appropriate to the problem domains we're dealing with, or should we be looking to simplify the structure and source code?

What's needed instead are some rules of thumb to help us identify code which we should simplify to achieve greater clarity or robustness.

## Incidental Complexity

There's another way we can categorize complexity: by making the distinction between *incidental* and *inherent* complexity (or sometimes *accidental* or *essential* complexity).  Here's the wikipedia definition:

> Accidental complexity: Relates to difficulties a programmer faces due to the chosen software engineering tools. A better fitting set of tools or a more high-level programming language may reduce it.

> Essential complexity: Is caused by the characteristics of the problem to be solved and cannot be reduced.

> <cite>[Wikipedia entry on *Programming complexity*](http://en.wikipedia.org/wiki/Programming_complexity#Types)</cite>

In my opinion, this isn't general enough.  Plenty of code features incidental complexity (i.e. that which is not inherent to solving the problem at hand) not because of the limitations of the tools, but because the implementation is not especially clean and includes unnecessary background noise.

### Red, yellow and green code

Reginald Braithwaite expresses this particular trait of poor code elegantly in his article [Economizing can be penny-wise and pound foolish](http://weblog.raganwald.com/2006/12/economizing-can-be-penny-wise-and.html):

> Pick up the source code for a program and three highlighter markers, one green, one yellow, and one red. Go through the source. If you don't understand what something does, mark it in red. If you understand it, but it has everything to do with the accidental difficulty of the implementation and nothing to do with the inherent difficulty of the problem, mark it in yellow. And finally, if something seems to express the problem and its solution fairly directly in an manner you understand, mark it in green.

> [...]

> When the green is all in once place, we can look at it and verify it and think about it with our *Inherent Difficulty* hat on. And by segregating all of the yellow code into its own place, we can look at it and verify it and think about it with our Accidental Difficulty or *Implementation* hat on.

Braithwaite here points out that, from the point of view of an application developer, library code is often reasoned about with the *accidental difficulty* hat on.  I would suggest there are two reasons why this might be the case.

First, as suggested by the Wikipedia definition of accidental complexity: sometimes the limitations of the tools we use introduce unnecessary complexity to the implementation.  This is unavoidable, but by ensuring such cases are confined to library code, our application can remain eminently readable.

Secondly, without due care, we might fail to correctly decompose our code into single responsibility classes or functions.  If we allow our code to take on multiple responsibilities then the purpose of each unit of code is diluted with distractions, as the developer is trying to solve too many problems at once.  Consider this example:

	function selectedItems(items) {
	    var results = [];
	    for (var k = 0; k < items.length; ++k) {
	        if (items[k].selected()) {
	            results.push(items);
	        }
	    }
	    return results;
	}

The problem is easy to summarize: *pick the items for which ```selected()``` holds true*.  But the implementation complexity is increased by the background noise of the loop, iteration variable and temporary ```results``` object.  The trouble is that we're trying to solve two problems simultaneously: the one where we identify selected items, and the one where we filter a list for a given predicate.

If we decompose our code to solve these two problems individually, the readability of both improves:

	function filter(items, predicate) {
	    var results = [];
	    for (var k = 0; k < items.length; ++k) {
	        if (predicate(items[k])) {
	            results.push(items);
	        }
	    }
	    return results;
	}
	
	function selectedItems(items) {
	    return filter(items, function(item) {
	        return item.selected();
	    });
	}

### Cohesion

A final cause of incidental complexity is low cohesion: by failing to group modules into functionally related units, we make it harder to identify the purpose of dependencies of our unit of code.

## Defining Incidental Complexity

So how then should we define this kind of complexity?

In a blog post, Peter Rosser summarizes it pretty well, though he calls it system complexity:

> System complexity is a property of a system that is directly proportional to the difficulty one has in comprehending the system at the level and detail necessary to make changes to the system without introducing instability or functional regressions.

> <cite>[Peter Rosser, *Thoughts on software complexity*](http://blogs.msdn.com/b/peterrosser/archive/2006/06/02/softwarecomplexity.aspx)</cite>

This seems a useful definition.  What's most critical to having a system which one can reason about, develop and maintain is to avoid local complexity.  If you can safely make local modifications, then it matters less how complex the whole application is: you don't need to understand the entire codebase all at once in order to develop new features.

However, I prefer a more succinct definition:

> Incidental complexity is anything which distracts from solving *the problem at hand*.

I prefer this formulation because it highlights (even without the emphasis) the multiplicity we should be concerned with: at any one point in our code, we should be solving for one problem, and we should be avoiding unnecessary background noise.  To summarize the points above, such complexity might arise for three reasons:

1. Unsuitability of the language/tools being used to solve the problem (perhaps because of limitations; perhaps because the wrong ones have been selected).
2. Insufficient decomposition of solutions into distinct units of code, leaving solutions to subproblems as background noise.
3. Low cohesion.

By avoiding incidental complexity, our code becomes easier to read and reason about, and easier to extend (and refactor in the future).  This is the kind of complexity it is most critical to avoid, and happily it's also one of the easiest to identify.
