---
layout: page
title: Home
id: home
permalink: /
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
            <i style="color:gray">{{date}}</i>:
        {%- endif -%}
        {%- if note.tags contains 'index' -%}
            <li style="padding-bottom: 0.6em; "><a href="{{note.url}}" style="color: #0B7E4A; font-weight:bold">{{ note.title }}</a></li>
        {%- elsif note.tags contains 'subpage' -%}
            
        {% else %}
            <li style="padding-bottom: 0.6em; "><a href="{{note.url}}">{{ note.title }}</a></li>
        {%- endif -%}        
        {% assign prevdate =  note.date | split: ' ' | first %}
    {%- endfor -%}
    <br/>
</main>