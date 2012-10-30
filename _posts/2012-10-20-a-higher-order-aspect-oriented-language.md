---
layout: post
title: A Higher-Order Aspect-Oriented Language
tags:
    - Programming Languages
    - AOP
---

For my masters thesis (back in 2008) I designed a strongly typed higher-order language with objects, and both static and dynamic aspects.  The project probably has limited appeal, but for sentimental value the thesis is available [here](http://cloud.github.com/downloads/jbrunton/obj-asp/report.pdf), and the implementation is [here](https://github.com/jbrunton/obj-asp).

One of the interesting features was that the full language is easily implementable in terms of a much simpler core language with objects, references and functions.  The thesis provides the appropriate translation, and demonstrates that type safety holds (i.e. that *progress* and *preservation* are both satisfied).
