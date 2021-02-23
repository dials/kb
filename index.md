## Welcome to the DIALS community knowledgebase

This is a loose collection of pages supplementing [the main DIALS documentation](https://dials.github.io/).

### Howtos

We have a set of howtos:

<div style="column-count:2"><ul>
{% for howto in site.howtos %}
<li><a href="{{ site.baseurl }}{{ howto.url }}">{{ howto.name }}</a></li>
{% endfor %}
</ul></div>

### Explanations

These documents aim to clarify and illuminate particular topics relating to DIALS code and to how DIALS is used.

<div style="column-count:2"><ul>
{% for explanation in site.explanations %}
<li><a href="{{ site.baseurl }}{{ explanation.url }}">{{ explanation.name }}</a></li>
{% endfor %}
</ul></div>

### Meeting minutes

These are minutes from the DIALS developer meetings, which are concerned with crystallography and tool development:

<div style="column-count:2"><ul>
{% for meeting in site.meetings %}
<li><a href="{{ site.baseurl }}{{ meeting.url }}">{{ meeting.name }}</a></li>
{% endfor %}
</ul></div>

### Project steering

There is a separate track of DIALS core meetings. Those are about releases, packaging, integration, and overall project direction.

<div style="column-count:2"><ul>
{% for meeting in site.core %}
<li><a href="{{ site.baseurl }}{{ meeting.url }}">{{ meeting.name }}</a></li>
{% endfor %}
</ul></div>

From time to time we discuss and decide on larger proposals &endash; akin to <a href="https://en.wikipedia.org/wiki/Request_for_Comments">RFCs</a>, <a href="https://www.python.org/dev/peps/">PEPs</a>, and <a href="https://numpy.org/neps/nep-0000.html">NEPs</a>.

<div style="column-count:2"><ul>
{% for meeting in site.proposals %}
<li><a href="{{ site.baseurl }}{{ proposal.url }}">{{ proposal.name }}</a></li>
{% endfor %}
</ul></div>

