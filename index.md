## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/dials/kb/edit/master/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Howtos

We have a set of howtos:
{% for howto in site.howtos %}
* <a href="{{ site.baseurl }}{{ howto.url }}">{{ howto.name }}</a> ({{ howto.path }} / {{ howto.relative_path }} / {{ site.baseurl }})
{% endfor %}

and again in markdown:
{% for howto in site.howtos %}
- [{{ howto.name }}]({{ site.baseurl }}{{ howto.url }})
{% endfor %}

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/dials/kb/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
