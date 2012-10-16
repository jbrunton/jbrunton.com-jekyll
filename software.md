---
layout: default
---
# Software
I work as a full-stack web and systems engineer across a variety of technologies, including RoR, JavaScript/CoffeeScript, and <a target="_blank" href="http://en.wikipedia.org/wiki/Tcl">something called TcL</a>.

My background includes .NET/WPF application development with Prism, and I have experience with a number of functional languages (including Haskell and OCaml), which is reflected in the way I solve programming problems.

Over the years I've also worked extensively with Java, Delphi/Object Pascal and C++, but I'm less excited by those today.

## Software Design

I'm particularly interested in the application of dependency injection in order to achieve highly testable and platform-independent code.  I've been experimenting with how best to combine proven design patterns for structuring JavaScript code with DI, and also a Rails-like DSL.  The project is available on github:

* [frappuccino-core](https://github.com/jbrunton/frappuccino-core) is the core framework gem, including a DI container, DSL for structuring domain models, and patterns for writing loosely coupled, modular applications.
* [frappuccino-demo](https://github.com/jbrunton/frappuccino-demo) is a demo application, with a readme which highlights some of the most notable features of the framework.  Take a look here for an introduction.

Also, here are some slides from presentations I've given on the subject of designing large-scale client-side applications:

* [JavaScript Best Practices](https://speakerdeck.com/u/jbrunton/p/javascript-best-practices)
* [Building Testable Large Scale Applications](https://speakerdeck.com/u/jbrunton/p/building-testable-large-scale-applications)

### Posts related to Software Design:

{% assign posts_list = site.tags['software design'] %}
{% include posts_list.html %}
