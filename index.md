## Welcome to the DIALS community knowledgebase

This is a loose collection of pages supplementing [the main DIALS documentation](https://dials.github.io/).

Pages hare are built automatically from [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/)
files in the [dials/kb](https://github.com/dials/kb) repository.
Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild these pages.

### Howtos

We have a set of howtos:

<div style="column-count:2">

{% for howto in site.howtos %}
- [{{ howto.name }}]({{ site.baseurl }}{{ howto.url }})
{% endfor %}

</div>

### Meeting minutes

These are minutes from the DIALS developer meetings, which are concerned with crystallography and tool development:

<div style="column-count:2">

{% for meeting in site.meetings %}
- [{{ meeting.name }}]({{ site.baseurl }}{{ meeting.url }})
{% endfor %}

</div>

There is a separate track of DIALS core meetings. Those are about releases, packaging, integration, and overall project direction.

<div style="column-count:2">

{% for meeting in site.core %}
- [{{ meeting.name }}]({{ site.baseurl }}{{ meeting.url }})
{% endfor %}

</div>
