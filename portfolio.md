layout: page
title: Portfolio
permalink: /portfolio/

<div class="portfolio-page">
<p>Some of the applications I have been working on.</p>

{% for project in site.portfolio %}
    <a href="{{ project.url }}">
        {{ project.title }}
    </a>
{% endfor %}

</div>