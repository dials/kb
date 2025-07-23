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

<div style="column-count:1"><ul>
{% for explanation in site.explanations %}
<li><a href="{{ site.baseurl }}{{ explanation.url }}">{{ explanation.name }}</a></li>
{% endfor %}
</ul></div>

### Project steering

These are minutes from the DIALS core meeting group. These meetings are about releases, packaging, integration, and overall project direction, but we also
discuss crystallography and tool development as the topics arise.

<div style="column-count:2"><ul>
{% for meeting in site.core reversed %}
<li><a href="{{ site.baseurl }}{{ meeting.url }}">{{ meeting.name }}</a></li>
{% endfor %}
</ul></div>

From time to time we discuss and decide on larger proposals – akin to <a href="https://en.wikipedia.org/wiki/Request_for_Comments">RFCs</a>, <a href="https://www.python.org/dev/peps/">PEPs</a>, and <a href="https://numpy.org/neps/nep-0000.html">NEPs</a>.

<div style="column-count:2"><ul>
{% for proposal in site.proposals %}
<li><a href="{{ site.baseurl }}{{ proposal.url }}">{{ proposal.number }}: {{ proposal.name }}</a></li>
{% endfor %}
</ul></div>

### Older Meeting minutes

These are minutes from the older DIALS developer meeting series, which were
concerned with crystallography and tool development. These discussions are
commonly held in the project steering group now.

<div style="column-count:2"><ul>
{% for meeting in site.meetings reversed %}
<li><a href="{{ site.baseurl }}{{ meeting.url }}">{{ meeting.name }}</a></li>
{% endfor %}
</ul></div>
