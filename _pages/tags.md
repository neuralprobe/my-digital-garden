---
layout: page
title: Tags
permalink: /tags/
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
    {% assign tags =  site.notes | map: 'tags' | join: ' '  | split: ' ' | uniq %}
    <b>Tags:
    {% for tag in tags %}
        <a class="internal-link" href="{{ site.baseurl }}/tags/#{{ tag }}">#{{ tag }}</a>
    {%- endfor -%}
    </b>
    <hr>
    {% for tag in tags %}
        <div id="{{ tag }}"><h3 id="{{ tag }}">{{ tag | capitalize }}</h3><br>
        {%- for note in site.notes -%}
            {%- if note.tags contains tag -%}
                <li style="padding-bottom: 0.6em; list-style: none;"><a href="{{note.url}}">{{ note.title }}</a></li> 
            {%- endif -%}
        {%- endfor -%}
        </div>
        <hr>
    {%- endfor -%}
    <br/>
    <br/>
</main>