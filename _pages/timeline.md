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
    {% assign prevdate =  '0000-00-00' %}
    {%- for note in notes -%}
        {% assign date =  note.date | split: ' ' | first %}
        {%- if date != prevdate -%}
            <i>{{date}}</i>:
        {%- endif -%}
        <li style="padding-bottom: 0.6em; "><a href="{{note.url}}">{{ note.title }}</a></li>
        {% assign prevdate =  note.date | split: ' ' | first %}
    {%- endfor -%}
    <br/>
</main>