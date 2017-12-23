---
layout: post
title: Ruby Gem Certificate Verify Failed Errors
excerpt:
categories: articles
tags: [ruby, gem, error]
comments: true
share: true
---

Error:

{% highlight text %}
Error fetching https://rubygems.org:
SSL_connect returned=1 errno=0 state=unknown state: certificate verify failed (https://s3.amazonaws.com/production.s3.rubygems.org/specs.4.8.gz)
{% endhighlight %}

To resolve this problem, perform the following commands respectively:

{% highlight bash %}
sudo gem sources -r https://rubygems.org/
sudo gem sources -a http://rubygems.org/
sudo gem update --system
sudo gem sources -r http://rubygems.org/
sudo gem sources -a https://rubygems.org/
{% endhighlight %}
