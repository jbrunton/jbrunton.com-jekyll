---
layout: post
title: A Higher-Order Aspect-Oriented Language
tags:
    - Programming Languages
    - AOP
---

For my masters degree I developed a simple language which looked at how one might usefully leverage these two paradigms together, providing a simple implementation of the proto-language.  The project probably has limited appeal, but for sentimental value the thesis is available [here](http://cloud.github.com/downloads/jbrunton/obj-asp/report.pdf), and the implementation is [here](https://github.com/jbrunton/obj-asp).

One of the interesting features was that the full language (with both static and dynamic aspects) is easily implementable in terms of a much simpler core language with objects, references and functions.  The thesis provides the appropriate translation, and demonstrates that type safety holds (i.e. that *progress* and *preservation* are both satisfied).
