---
layout: post
title: Introducing Frappuccino
tags:
    - Software Design
    - Software Testability
    - Dependency Injection
---

[Frappuccino](http://jbrunton.github.com/frappuccino-core/) is a framework I've been developing to explore best practices for writing highly testable, loosely coupled, large scale applications in JavaScript/CoffeeScript (both for client and server applications).

The sources of inspiration include:

* Addy Osmani's excellent work on design patterns and practices for the client, including [Patterns For Large-Scale JavaScript Application Architecture](http://addyosmani.com/largescalejavascript/) and related [jQuery Conf slides](http://addyosmani.com/blog/jqcon-largescalejs-2012/), which describe some extremely useful patterns for a modular, loosely coupled application architecture.
* Microsoft's [Prism framework and guidance](http://compositewpf.codeplex.com/), which advocates a number of related patterns, and also makes heavy use of dependency injection.
* Ruby on Rails, for its emphasis on opinionated DSL's and DRYness.

The name Frappuccino comes from the fact that the code is written in CoffeeScript, and (though I do say so myself) I think it's pretty cool.

The code (very much work in progress) is available [here](https://github.com/jbrunton/frappuccino-core).  Of more interest to those who are curious, however, is this [demo application on github](https://github.com/jbrunton/frappuccino-demo).  Check out the [readme](https://github.com/jbrunton/frappuccino-demo#readme) for an overview of some of the most interesting features of the framework, and try it out online [here](frappuccino-demo.com).