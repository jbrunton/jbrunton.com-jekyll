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

There are also plenty of theoretical metrics available to help us measure the complexity of our programs:

* [Cyclomatic complexity](http://en.wikipedia.org/wiki/Cyclomatic_complexity) is a measure of the number of execution paths through a program.
* [Halstead metrics](http://en.wikipedia.org/wiki/Halstead_complexity_measures) give figure for the *length* and *vocabulary* of a program (and, although originally conceived for procedural programs, can easily be [adapted for OOP languages](http://www.virtualmachinery.com/sidebar2.htm)).

But how useful are these definitions in practice?  Is it really useful to put a figure on the complexity of your application?  It's probably clear that your multi-platform web service (complete with mobile apps and SMS notifications) has a higher degree of complexity than the rake task which deploys it &mdash; but how does that help you make informed decisions about the structure of the application?  Is the level of complexity to appropriate to the problem domains we're dealing with, or should we be looking to simplify the structure and source code?

What's needed instead are some rules of thumb to help us identify code which we should simplify to achieve greater clarity or robustness.

## Incidental Complexity

There's another way we can categorize complexity: by making the distinction between *incidental* and *inherent* (or sometimes *accidental* or *essential*) complexity.  Here's the wikipedia definition:

> Accidental complexity: Relates to difficulties a programmer faces due to the chosen software engineering tools. A better fitting set of tools or a more high-level programming language may reduce it.

> Essential complexity: Is caused by the characteristics of the problem to be solved and cannot be reduced.

<cite>[Wikipedia &mdash; Programming complexity](http://en.wikipedia.org/wiki/Programming_complexity#Types)</cite>

In my opinion, however, this isn't general enough.  Plenty of code features incidental complexity (i.e. that which is not inherent to solving the problem at hand) not because the solution could be better written in another language, but because the developer is trying to solve too many problems at once.  Consider this example:

{% highlight javascript %}
function selectedItems(items) {
    var results = [];
    for (var k = 0; k < items.length; ++k) {
        if (items[k].selected()) {
            results.push(items);
        }
    }
    return results;
}
{% endhighlight %}

{% highlight javascript %}
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
{% endhighlight %}

In a blog post, Peter Rosser describes a related kind of complexity:

> System complexity is a property of a system that is directly proportional to the difficulty one has in comprehending the system at the level and detail necessary to make changes to the system without introducing instability or functional regressions.

> <cite>[Peter Rosser, *Thoughts on software complexity*](http://blogs.msdn.com/b/peterrosser/archive/2006/06/02/softwarecomplexity.aspx)</cite>

Here we're getting somewhere.  What's most critical to having a system which one can reason about, develop and maintain is to avoid local complexity.  If you can safely make local modifications, then it matters less how complex the whole application is &mdash; you don't need to understand the entire codebase all at once in order to develop new features.

I prefer a more succinct definition for this kind of incidental complexity, however:

> Incidental complexity is anything which distracts from solving *the problem at hand*.

I prefer this formulation because it highlights (even without the emphasis) the multiplicity we should be concerned with: at any one point in our code, we should be solving for one problem.

---

And as we have seen, there are two/three (?) main causes of incidental complexity.  One may be that you're using the wrong algorithm, data structure or language to solve the problem domain.  However, your choice of language may be beyond your control.  (sometimes, it may be that an unnecessary level of abstraction has been reached &mdash; or an unnecessarily disruptive design pattern has been selected.  this often a reflection of the limitations of the language - e.g. visitor pattern isn't required in a language with pattern matching or multiple dispatch)

The other cause is that the the solving of other problems distracts from the solution at hand.  This is something you, as a developer, always have control over.

Reginald Braithwaite expresses this particular problem most elegantly in his article [Economizing can be penny-wise and pound foolish](http://weblog.raganwald.com/2006/12/economizing-can-be-penny-wise-and.html)

> Pick up the source code for a program and three highlighter markers, one green, one yellow, and one red. Go through the source. If you don’t understand what something does, mark it in red. If you understand it, but it has everything to do with the accidental difficulty of the implementation and nothing to do with the inherent difficulty of the problem, mark it in yellow. And finally, if something seems to express the problem and its solution fairly directly in an manner you understand, mark it in green.

> ...

> When the green is all in once place, we can look at it and verify it and think about it with our *Inherent Difficulty* hat on. And by segregating all of the yellow code into its own place, we can look at it and verify it and think about it with our Accidental Difficulty or *Implementation* hat on.

Braithwaite here makes a few important assertions: one, that from the context of an application developer, library code is often reasoned about with the *accidental difficulty* hat on.  This is probably fair to say: most web engineers don't have to write sorting algorithms on a regular basis, so I'd sure be scratching my head pondering the correctness of a merge sort algorithm if I had to review the implementation of one.  But in fact, someone who works on scientific modeling might find the reverse true, and might categorize the merge sort as green code, and a simple AJAX cycle as yellow.  The point is that &mdash; providing we have the requisite background knowledge about the problem we're solving for &mdash; any solution which doesn't introduce unnecessary background noise (by trying to solve different problems simultaneously) has the potential to be green: and yellow and red code is merely what happens when this noise hits unacceptable levels.


By avoiding incidental complexity, our code becomes easier to read and reason about, and easier to extend (and refactor in the future).  This is the kind of complexity it is most critical to avoid, and happily it's also one of the easiest to identify.


---
incidental complexity:
- incorrect choice of algorithm/solution/data structure
  - too abstract (incorrect design pattern)
- language limitations (design patterns)
- complexity incidental to solution of problem (i.e. solving multiple problems simultaneously)

note: avoid incidental complexity, and the others will follow?
- solving problems individually => much easier to avoid complex branching after decomposing problem (may not reduce overall cyclomatic complexity of application- but reduces it for individual functions, making each easier to reason about individually)
- by decomposing problems, we can increase test coverage and robustness (see writing testable code)
- by decomposing problems, we make it easier to refactor or generalize individual units of code in the future, without cutting across other functional areas.
