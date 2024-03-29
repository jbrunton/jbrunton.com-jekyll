---
layout: default
title: Programming Languages
---

<div class="clearfix">
    <div class="two columns">
        <h1>Programming Languages</h1>
    </div>
</div>
<div class="clearfix">
    <div class="two columns">
        <p>To write expressive code, it's important to choose the correct language for the job at hand, and also to understand how to use it correctly.  A good understanding of the underlying theory goes a long to being able to make informed decisions in this space.
        
        <h2>A Higher-Order Aspect-Oriented Language</h2>
        
        <p>For my masters degree I developed a simple language which looked at how one might reconcile and implement these distinct paradigms, providing a simple implementation of the proto-language: the thesis is available <a href="http://cloud.github.com/downloads/jbrunton/obj-asp/report.pdf">here</a>, and the implementation is <a href="https://github.com/jbrunton/obj-asp">here</a>.
    </div>
    
    <div class="one column">

        <p>Posts related to Programming Languages:</p>
        
        {% assign posts_list = site.tags['Programming Languages'] %}
        {% include posts_list.html %}

    </div>
</div>
