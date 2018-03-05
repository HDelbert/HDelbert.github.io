---
layout: post
title: "Markdown Sample"
date: 2015-11-17 23:12:20 +08:00
tags: markdown
---

A First Level Header
====================

A Second Level Header
---------------------

Now is the time for all good men to come to
the aid of their country. This is just a
`regular paragraph`.

The quick brown fox jumped over the lazy
dog's back.

### Header 3

> This is a blockquote.
>
> This is the second paragraph in the blockquote.
>
> ## This is an H2 in a blockquote

### PHRASE EMPHASIS

**Markdown:**

Some of these words *are emphasized*.
Some of these words _are emphasized also_.

Use two asterisks for **strong emphasis**.
Or, if you prefer, __use two underscores instead__.

**Output:**

{% highlight xml %}
<p>Some of these words <em>are emphasized</em>.
Some of these words <em>are emphasized also</em>.</p>

<p>Use two asterisks for <strong>strong emphasis</strong>.
Or, if you prefer, <strong>use two underscores instead</strong>.</p>
{% endhighlight %}

### LISTS
Unordered (bulleted) lists use asterisks, pluses, and hyphens (*, +, and -) as list markers. These three markers are interchangable; this:

*   Candy.
*   Gum.
*   Booze.

this:

+   Candy.
+   Gum.
+   Booze.

and this:

-   Candy.
-   Gum.
-   Booze.

all produce the same output:

{% highlight xml %}
<ul>
<li>Candy.</li>
<li>Gum.</li>
<li>Booze.</li>
</ul>
{% endhighlight %}

Ordered (numbered) lists use regular numbers, followed by periods, as list markers:

{% highlight xml %}
1.  Red
2.  Green
3.  Blue
{% endhighlight %}

**Output:**

{% highlight xml %}
<ol>
<li>Red</li>
<li>Green</li>
<li>Blue</li>
</ol>
{% endhighlight %}

If you put blank lines between items, you’ll get <p> tags for the list item text. You can create multi-paragraph list items by indenting the paragraphs by 4 spaces or 1 tab:

*   A list item.

    With multiple paragraphs.

*   Another item in the list.

**Output:**

{% highlight xml %}
<ul>
<li><p>A list item.</p>
<p>With multiple paragraphs.</p></li>
<li><p>Another item in the list.</p></li>
</ul>
{% endhighlight %}

### LINKS

Markdown supports two styles for creating links: inline and reference. With both styles, you use square brackets to delimit the text you want to turn into a link.

Inline-style links use parentheses immediately after the link text. For example:

This is an [example link](http://example.com/).

**Output:**

{% highlight xml %}
<p>This is an <a href="http://example.com/">
example link</a>.</p>
{% endhighlight %}

Optionally, you may include a title attribute in the parentheses:

This is an [example link](http://example.com/ "With a Title").

**Output:**

{% highlight xml %}
<p>This is an <a href="http://example.com/" title="With a Title">
example link</a>.</p>
{% endhighlight %}

Reference-style links allow you to refer to your links by names, which you define elsewhere in your document:

I get 10 times more traffic from [Google][1] than from
[Yahoo][2] or [MSN][3].

[1]: http://google.com/        "Google"
[2]: http://search.yahoo.com/  "Yahoo Search"
[3]: http://search.msn.com/    "MSN Search"

**Output:**

{% highlight xml %}
<p>I get 10 times more traffic from <a href="http://google.com/"
title="Google">Google</a> than from <a href="http://search.yahoo.com/"
title="Yahoo Search">Yahoo</a> or <a href="http://search.msn.com/"
title="MSN Search">MSN</a>.</p>
{% endhighlight %}

The title attribute is optional. Link names may contain letters, numbers and spaces, but are not case sensitive:

I start my morning with a cup of coffee and

[The New York Times][NY Times].

[ny times]: http://www.nytimes.com/

**Output:**

{% highlight xml %}
<p>I start my morning with a cup of coffee and
<a href="http://www.nytimes.com/">The New York Times</a>.</p>
{% endhighlight %}

### IMAGES

Image syntax is very much like link syntax.

Inline (titles are optional):

![alt text](http://lorempixel.com/400/200/ "Title")

Reference-style:

{% highlight xml %}
![alt text][id]

[id]: http://lorempixel.com/400/200/ "Title"
{% endhighlight %}
Both of the above examples produce the same output:

{% highlight xml %}
<img src="/path/to/img.jpg" alt="alt text" title="Title" />
{% endhighlight %}
