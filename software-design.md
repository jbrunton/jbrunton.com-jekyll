---
layout: default
---
# Software Design

The benefits of well designed code are clear: good code is that which is readable, maintainable and testable.  And code with such qualities has very real benefits for a business: it's cheaper to development, safer to change, and easier to hire and on-board new engineers.

I'm interested in the theory of software design, but I'm also always striving to find ways to make it intuitive and to easily put it into practice.  As such, a lot of the posts on this site endeavor to distil the theory down to pragmatic rules-of-thumb to help one easily identify good coding practices from those which will be costly.

## Dependency Injection

I'm particularly interested in the application of dependency injection in order to achieve highly testable and platform-independent code.  I've been experimenting with how best to combine proven design patterns for structuring JavaScript code with DI, and also a Rails-like DSL.  The project is available on github:

* [frappuccino-core](https://github.com/jbrunton/frappuccino-core) is the core framework gem, including a DI container, DSL for structuring domain models, and patterns for writing loosely coupled, modular applications.
* [frappuccino-demo](https://github.com/jbrunton/frappuccino-demo) is a demo application, with a readme which highlights some of the most notable features of the framework.  Take a look here for an introduction.

## Presentations

Here are some slides from presentations I've given on the subject of designing large-scale client-side applications:

* [JavaScript Best Practices](https://speakerdeck.com/u/jbrunton/p/javascript-best-practices)
* [Building Testable Large Scale Applications](https://speakerdeck.com/u/jbrunton/p/building-testable-large-scale-applications)

## Posts related to Software Design:

{% assign posts_list = site.tags['Software Design'] %}
{% include posts_list.html %}
