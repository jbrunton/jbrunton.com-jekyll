---
layout: default
title: Software Design
---
<div class="clearfix">
    <div class="two columns">
        <h1>Software Design</h1>
    </div>
</div>
<div class="clearfix">
    <div class="two columns">
       
        <p>The benefits of well designed code are clear: good code is that which is readable, maintainable and testable.  And code with such qualities has very real benefits for a business: it's cheaper to develop, safer to change, and easier to hire and on-board new engineers.
        
        <p>I'm interested in the theory of software design, but I'm always thoroughly concerned with the practical: how can abstract notions, such as complexity, be made intuitive easily put it into practice.  As such, a lot of the posts on this site endeavor to distil the theory down to pragmatic rules-of-thumb to help one easily identify good coding practices from those which will be costly.
        
        <h2>Dependency Injection</h2>
        
        <p>I'm particularly interested in the application of dependency injection in order to achieve highly testable and platform-independent code.  I've been experimenting with how best to combine proven design patterns for structuring JavaScript code with DI, and also a Rails-like DSL.  The project is available on github:
        
        <ul>
            <li><a href="https://github.com/jbrunton/frappuccino-core">frappuccino-core</a> is the core framework gem, including a DI container, DSL for structuring domain models, and patterns for writing loosely coupled, modular applications.
            <li><a href="https://github.com/jbrunton/frappuccino-demo">frappuccino-demo</a> is a demo application, with a readme which highlights some of the most notable features of the framework.  Take a look here for an introduction.
        </ul>
        
        <h2>Presentations</h2>
        
        <p>Here are some slides from presentations I've given on the subject of designing large-scale client-side applications:
        
        <ul>
            <li><a href="https://speakerdeck.com/u/jbrunton/p/javascript-best-practices">JavaScript Best Practices</a>
        <li><a href="https://speakerdeck.com/u/jbrunton/p/building-testable-large-scale-applications">Building Testable Large Scale Applications</a>
        </ul>

    </div>
    <div class="one column">

        <p>Posts related to Software Design:</p>
        
        {% assign posts_list = site.tags['Software Design'] %}
        {% include posts_list.html %}

    </div>
</div>