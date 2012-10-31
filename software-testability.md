---
layout: default
title: Software Testability
---
<div class="clearfix">
    <div class="two columns">
        <h1>Software Testability</h1>
    </div>
</div>
<div class="clearfix">
    <div class="two columns">
        There are two incredibly compelling reasons why you should write tests:
        
        <ul>
            <li>Your code will be safer to extend and refactor, reducing the cost of development down the line.
            <li>Well structured tests will improve the quality of your code in other respects, by highlighting flaws such as tight coupling.
        </ul>
        
        There is a further, even more compelling reason to write tests before you even start your implementation:
        
        <ul>
            <li>A BDD approach to development will help you better structure your code and define cleaner, more intuitive interfaces for component classes and objects.
        </ul>
    </div>
    <div class="one column">
        <p>Posts related to Software Testability:</p>

        {% assign posts_list = site.tags['Software Testability'] %}
        {% include posts_list.html %}
    </div>
</div>
