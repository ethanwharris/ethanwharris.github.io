---
layout: post-links
title:  "Internal Links on GitHub Pages with Jekyll"
excerpt: "Using pure Liquid to add internal links to header tags on GitHub pages"
date:   2017-11-26 16:00:00
mathjax: false
published: false
comments: true
---

# The Problem

When I started building this website a few weeks ago, I can remember being immediately impressed with the capabilities Jekyll has to offer. For a while, everything included in vanilla Jekyll was sufficient to keep me entertained and get everything out of a blog that I had hoped for. One frustration, however, was the slow realisation that only a few Jekyll plugins are supported on GitHub pages. There is a wealth of content out there for Jekyll but only a small subset of it can be used on GitHub. This criticism isn’t entirely fair. In truth, you can create a Jekyll site and then build locally, before pushing the static output to the master branch of your blog or gh-pages branch of your project if you wish. Hopefully, the reasons for me not wanting to do this are pretty clear. One of the main motivations for using GitHub pages to compile Jekyll is that I am abstracted away from the build process. If I want to build locally then I can, otherwise, I don’t have to.

The first time I noticed the plugin problem was when I started looking for ways to minify HTML on my website. If you’re wondering why I was bothering to try to minify HTML for an already static site with (at this point) no content, you are correct to do so. In truth, I was a bit bored at the time and started to unearth the whole world behind Google PageSpeed, minification, caching and cloudflare. Specifically, I wanted to know if I could minify the content of my site and boost (albeit needlessly) my Google PageSpeed score. As you might expect, many plugins exist for Jekyll which can perform this task. However, none of them are supported on GitHub pages for somewhat well founded security reasons. Rather predictably, the people at GitHub aren't exactly thrilled by the idea of people being able to run arbitrary code on their servers. You can find a list of plugins which are supported on GitHub pages [here](https://pages.github.com/versions).

The search was looking pretty bleak until I stumbled upon the [jekyll-compress-html](https://github.com/penibelst/jekyll-compress-html) layout which uses pure Liquid to compress HTML. I won't go into this too much as there is a wealth of content online relating to increasing your Google PageSpeed score with Jekyll on GitHub pages. What interested me was the idea of using Liquid to bring some enhanced functionality to Jekyll. In case you don't know, Liquid is the language used by Jekyll to turn layouts and posts into a fully-fledged website. Liquid includes a host of basic programming language components such as variable assignment, looping and conditionals. An extremely useful cheat sheet for Liquid can be found [here](https://github.com/Shopify/liquid/wiki/liquid-for-designers).

## Internal Links

A little later on, I decided that it would be nice if headers in my blog posts had icons next to them which linked directly to that part of the post. There are many javascript solutions to this problem. Typically, they involve running some javascript when the page loads that loops over all of the header elements and adds an appropriate HTML tag to the content. This solution isn't exactly pretty as it is slow and not in keeping with the general principle of statically generated sites. We could write a Jekyll plugin to solve the problem but as already stated, this isn't ideal either. However, there is a third option. Liquid.

# The Solution

First I'll give the solution I arrived at, in the form of a gist containing the template file used for this site. Then I'll give an overview of the code and how it works. The main motivation for writing this post is that, after my initial elation to find the jekyll-compress-html template, I was perturbed to discover that almost no other such tools exist. Where I had hoped to find a whole ecosystem of ways to spruce up a GitHub pages site, I found very little.

{% gist ethanwharris/73399af55b5995507e2681025d62d91c %}
	
## User Guide

If you want to use the template in your blog, follow these steps:
- Save the code from the gist in an HTML file in your _layouts directory
- Edit the layout:post line to reference your post layout
- In your posts, edit the layout:... line to point to the internal-links file
- Edit the HTML code in line 28 to be whatever markup you desire for your links

# Code Overview

{% highlight liquid %}
{% raw %}
---
layout: post
---
{% endraw %}
{% endhighlight %}

In the first part, we point to the post layout. The template needs to be here in the hierarchy or the links will be added to all headers. Including disqus comments, the title etc.

{% highlight liquid %}
{% raw %}
{% capture _content %}
  {{ content }}
{% endcapture %}

{% assign _headers = "h1 h2 h3 h4 h5" | split: " " %}
{% endraw %}
{% endhighlight %}

This next bit uses the capture block to assign all of the content from the child page to a variable called _content. We then put together a list of HTML tags we are concerned with. To do this, we can just list the tags separated by spaces and then use the split filter to create a list which we can assign to a variable called _headers.

{% highlight liquid %}
{% raw %}
{% for _header in _headers %}
  {% capture _tag %}</{{ _header }}>{% endcapture %}
  {% assign _lines = _content | split: _tag %}
  {% assign _newcontent = "" %}
  {% assign _lastline = _lines | last %}
{% endraw %}
{% endhighlight %}

Here we loop over each of the headers and begin by splitting the content by the associated HTML close tag. We then also store the last line of the content. We have now obtained a list where each item apart from the last ends just before a header close block should appear.

{% highlight liquid %}
{% raw %}
  {% for _line in _lines %}
    {% unless _line == _lastline %}
      {% assign _id = _line | split: 'id="' | last | split: '"' | first %}
      {% assign _link = ' <a href="#' | append: _id | append: '"><i class="head-link fa fa-link"></i></a>' %}
      {% assign _newcontent = _newcontent | append: _line | append: _link | append: _tag %}
{% endraw %}
{% endhighlight %}

Now we simply concatenate each item with the code we wish to append to header elements and the close tag we lost in the splitting. However, we need to also obtain the id from the header tag so that we know where to link to. We assign a variable _id which contains this. Thankfully, kramdown gives each header an id by default. We split on 'id="' and get the very last element, then we split on '"' and get the first element. This gives us the id declared nearest to the header close tag we split on earlier. There is probably no guarantee that this will be the id we want, but in most cases we should be safe.

{% highlight liquid %}
{% raw %}
    {% endunless %}
  {% endfor %}
  {% assign _content = _newcontent | append: _lastline %}
{% endfor %}
{% endraw %}
{% endhighlight %}

Finally, we end the loops and re-assign the _content variable to include the added links for this particular header, remembering to replace the last line.

{% highlight liquid %}
{% raw %}
{{ _content }}
{% endraw %}
{% endhighlight %}

In the last step we display the content so that it can be forwarded to the next template in the chain.

# Final Remarks

So, that concludes this post. I suppose my aim here is to try to spread the idea of using Jekyll templates as a way of doing more with GitHub pages without having to resort to building locally, or using javascript. Ultimately, build times of a static website aren't all that important. After all, it's GitHub server time I'm wasting.
