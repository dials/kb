## Welcome to the DIALS community knowledgebase

This is a loose collection of pages supplementing [the main DIALS documentation](https://dials.github.io/).

### Howtos

We have a set of howtos:

<div style="column-count:2"><ul>
{% for howto in site.howtos %}
<li><a href="{{ site.baseurl }}{{ howto.url }}">{{ howto.name }}</a></li>
{% endfor %}
</ul></div>

### Meeting minutes

These are minutes from the DIALS developer meetings, which are concerned with crystallography and tool development:

<div style="column-count:2"><ul>
{% for meeting in site.meetings %}
<li><a href="{{ site.baseurl }}{{ meeting.url }}">{{ meeting.name }}</a></li>
{% endfor %}
</ul></div>

There is a separate track of DIALS core meetings. Those are about releases, packaging, integration, and overall project direction.

<div style="column-count:2"><ul>
{% for meeting in site.core %}
<li><a href="{{ site.baseurl }}{{ meeting.url }}">{{ meeting.name }}</a></li>
{% endfor %}
</ul></div>

