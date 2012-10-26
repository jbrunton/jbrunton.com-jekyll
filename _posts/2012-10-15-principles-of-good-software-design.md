---
layout: post
title: Principles of Good Software Design
tags:
    - Software Design
    - Don't Repeat Yourself
    - Divide & Conquer
---


- Complexity in software is perceived as the number 
of interacting elements within a software system, the 
internal structure of these elements, and the number 
and nature of interdependencies among them [R. Taylor, N. Medvidovic and E. Dashofy,  Software 
Architecture: Foundations, Theory and Practice, 
Hoboken, NJ: John Wiley & Sons, 2009.]

- definition of complexity?
  - variation - see 'SOFTWARE  COMPLEXITY  MEASUREMENT ' [http://sunnyday.mit.edu/16.355/kearney.pdf] and Wikipedia [http://en.wikipedia.org/wiki/Programming_complexity]
- definition of incidental/accidental complexity?
  - several - maybe conflicting?
    - http://weblog.raganwald.com/2006/12/economizing-can-be-penny-wise-and.html
    - 'Relates to difficulties a programmer faces due to the chosen software engineering tools' [wiki]
    - 'readable codebase' [http://infiniteundo.com/post/19910636303/intrinsic-and-incidental-complexity]
  - consider what is most important to readability and maintainability: incidental _to the problem at hand_
    - BUT consider: over-abstracted nature of e.g. Java enterprise apps.  unnecessary generality or over-abstraction - how to defined?  too far removed from problem domain - introduces further incidental complexity (compare with the kind introduced by design patterns?).
(  - what are the others?  are any others relevant?  maybe note distinct related types of incidental complexity.)

Over the years I've tried to distil the ideas I've had which, in my opinion, underpin all good software design.  Irrespective of the language, framework or codebase you're working across, well designed code should always respect these principles:

## 1. Avoid incidental complexity.

Or, in full:

> Avoid incidental complexity in application code in order to focus on solving the inherent complexity of the problem at hand.

http://weblog.raganwald.com/2006/12/economizing-can-be-penny-wise-and.html

* For code which is literally

This can be stated in a few equivalent ways:

> Each unit of code should solve one problem at a time

Each unit of code should solve one problem at a time.  (This can also be phrased as: avoid incidental complexity in application code in order to focus on solving the inherent complexity of the problem at hand.)

> 2. Each problem should be solved only once.

In other words: the [DRY](http://en.wikipedia.org/wiki/Don't_repeat_yourself) principle.)

> 3. Code without tests is already broken.

Or at least, it will be soon.

## Avoiding incidental complexity

The last two of these principles (write DRY code and write tests) have been well understood by the software community for a long time.

The first is a little more subtle, and I'm not aware of a catchy name for the principle, so I'll refer to it as: "avoid incidental complexity".

- explain what it means
- explain equivalence.

In effect, what it says is that clear, maintainable software should avoid polluting a unit of code with "noise" - all the code should be concerned with solving the problem at hand, rather than the incidental quirks of the particular implementation.

Why?
- likely to be reused in future - and more likely to be reusable if a general interface is defined (e.g. having a filter function, as opposed to iteration and boolean check)
- likely to be more testable - by factoring out such code, you can test it in isolation (and this provide better code coverage which will flag problems more directly)
- clarity - break down the problem into individual components and solve them individually

clearly decomposing our code into distinct solutions to well-defined problems.  thus, each can be written, tested, generalized and reasoned about individually.  this is the crux of making code maintainable and refactorable, and also helps us strike the right balance between how much to refactor right now: the answer is in fact to ensure separation between problems in the first place, each of which can then be generalized, and commonalities factored out, in isolation.

For example, consider this JavaScript function:

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

The problem the function is solving for is to filter a list to exclude items for which ```selected()``` evaluates to false.  (It should be fairly clear where this example is going, but bear with me while I emphasize the key motivations for refactoring here.)

The problem with the implementation of this function is that there's plenty of _incidental complexity_ to it: the ```results``` variable, the iteration loop and variable and the building of the array are all incidental to the way in which we build the filtered list, and not inherent to the problem of specifying a way to filter.  Thus our code suffers from two problems:

1. The signal-to-noise ratio is high: our code is far from being as concise as it could be, because of the incidental complexity.

2. Filtering is a pretty useful operation: if we factor it out, we can test the code in isolation, and make the function available for reuse in the future.

Thus, we end up with two functions, both of which are solving a single problem:

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

The benefits are then:

* We can reuse ```filter``` across other units of code.
* Our implementation of ```selectedItems``` is clearer (as will be any other client sites of ```filter```).
* We can test the two functions in isolation - and thus it will be easier to identify the nature of any test failures.

- one of the skills a good engineer should acquire is seeing how to decompose a problem into its component parts.  this is why some exposure to functional programming can be useful: it helps one see how to literally compose functions.

## A Corollary: Design Patterns

In light of this, it's interesting to note that use of design patterns sometimes introduces undesirable, incidental complexity to a codebase.  Sometimes this may be unavailable, and may be a reflection of the limits of the language being used.  But frequently it should be possible to make the use of design patterns more transparent by hiding them below more readable interfaces.

We can at this point pause to consider an important differentiator between design patterns: whether or not they are introducing incidental complexity to a problem, because they are addressing the limits of the underlying language (e.g. the visitor pattern, acting as a poor man's multiple-dispatch - which is never needed in a language with pattern matching).  Contrast this with the MVC pattern, which serves as a pattern to structure an application in a logical, loosely coupled manner, and you can see these two patterns are in fact performing very different functions.
