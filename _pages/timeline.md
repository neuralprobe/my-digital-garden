---
layout: page
title: Timeline
permalink: /timeline/
content-type: eg
---

<style>
.category-content a {
    text-decoration: none;
    color: #4183c4;
}

.category-content a:hover {
    text-decoration: underline;
    color: #4183c4;
}
</style>

<main>
    {% assign notes = site.notes | sort: 'date' | reverse %}
    {%- for note in notes -%}
        {% assign date =  note.date | split: ' ' | first %}
        <i>{{date}}</i>:
        <li style="padding-bottom: 0.6em; list-style: none;"><a href="{{note.url}}">{{ note.title }}</a></li> 
    {%- endfor -%}
    <br/>
</main>