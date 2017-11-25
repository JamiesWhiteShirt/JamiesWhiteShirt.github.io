---
# You don't need to edit this file, it's empty on purpose.
# Edit theme's home layout instead if you wanna make some changes
# See: https://jekyllrb.com/docs/themes/#overriding-theme-defaults
layout: default
---

There are plenty of tutorials around. Tutorials are often akin to cookbook recipes; They focus on the _how_ and less on the _why_.
Those tutorials are great when you need something done quickly, but they do not always contribute to your understanding.

These resources are the inverse. They focus on the _why_, and less on the _how_. If this is not your cup of tea, you should look for other resources.

## With that said, here are all my resources:
{% for resource in site.resources %}
- **[{{ resource.title }}]({{ resource.url }})** {{ resource.excerpt | remove: '<p>' | remove: '</p>' }}
{% endfor %}

----

If you would like to see a topic covered, please [create a new issue]({{ site.github.repository_url }}/issues).
