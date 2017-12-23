---
layout: post
title: "Ruby Gem Certificate Verify Failed Errors"
excerpt:
categories: articles
tags: [ruby, gem, ssl]
modified:
comments: true
share: true
---

Error:

{% highlight text %}
Error fetching https://rubygems.org:
SSL_connect returned=1 errno=0 state=unknown state: certificate verify failed (https://s3.amazonaws.com/production.s3.rubygems.org/specs.4.8.gz)
{% endhighlight %}

Perform the following command to resolve this problem:

{% highlight bash %}

$ curl -fsSL curl.haxx.se/ca/cacert.pem -o "$(ruby -ropenssl -e 'puts OpenSSL::X509::DEFAULT_CERT_FILE')"
{% endhighlight %}

**Reference:** [https://gist.github.com/mislav/5026283](https://gist.github.com/mislav/5026283)

Another solution:

{% highlight bash %}

$ wget https://github.com/rubygems/rubygems/releases/download/v2.2.3/rubygems-update-2.2.3.gem

$ sudo gem install rubygems-update-2.2.3.gem

$ sudo update_rubygems

$ sudo gem uninstall rubygems-update -x
{% endhighlight %}

**Reference:** [https://gist.github.com/luislavena/f064211759ee0f806c88](https://gist.github.com/luislavena/f064211759ee0f806c88)
