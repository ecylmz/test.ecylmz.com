---
layout: post
title: 'Ubuntu Canon Driver İnstall'
excerpt:
categories: articles
tags: [ubuntu, canon, driver]
comments: true
share: true
---

I have Canon i-SENSYS  MF4870dn printer.

Canon 64 bit driver not working properly on Ubuntu 14.04-64bit. After many
trials i found a method.

- Download a this [driver](http://www.canon-europe.com/Support/Consumer_Products/products/Fax__Multifunctionals/Laser/LaserBase_MF_series/i-SENSYS_MF4870dn.aspx?type=download&softwaredetailid=tcm:13-1185184&os=&language=)

- Extract the zip file

- go to `deutsch/32-bit_Driver/Debian`

- Then run this command:

{% highlight bash %}
        $ sudo dpkg -i cnd*
{% endhighlight %}

  You should have received a few errors. This is normal.

- command:

{% highlight bash %}
        $ sudo apt-get install -f
{% endhighlight %}

  Issue sudo apt-get install -f and package manager
  should offer you to install all previously required dependencies.

- Now, we remove the 32 bit drivers and install the 64bit drivers.

{% highlight bash %}
$ sudo dpkg -r cndrvcups-ufr2-uk
$ sudo dpkg -r cndrvcups-common
$ cd ../../64-bit_Driver/Debian
$ sudo dpkg -i cnd*
{% endhighlight %}

That's all. Thanks(!) Canon.
