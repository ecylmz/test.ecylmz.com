---
layout: post
title: "SSMTP Kurulumu ve Yapılandırması"
categories: articles
excerpt: "SMTP üzerinden mail gönderimini kolaylaştıran komut satırı aracı"
tags: [smtp, mail, howto]
comments: true
share: true
---

## Kurulum

{% highlight bash %}
$ sudo apt-get install ssmtp
{% endhighlight %}

## Yapılandırma

{% highlight bash %}
$ sudo vim /etc/ssmtp/ssmtp.conf
{% endhighlight %}

**Yandex Mail için yapılandırma**

{% highlight text %}
mailhub=smtp.yandex.ru:587
rewriteDomain=example.com
AuthUser=mail@example.com
AuthPass=P4ssw0rd
FromLineOverride=NO
UseTLS=YES
UseSTARTTLS=YES
{% endhighlight %}

---

{% highlight bash %}
$ sudo vim /etc/ssmtp/revaliases
{% endhighlight %}

{% highlight text %}
root:mail@example.com:smtp.yandex.ru:587
{% endhighlight %}

## Test

{% highlight text %}
echo "Test" | mail -s "Foo" youremail@example.com
{% endhighlight %}
