---
layout: post
title: "Ubuntu Docker Upgrade"
excerpt:
categories: articles
tags: [ubuntu, docker, upgrade]
modified:
comments: true
share: true
---

Ubuntu 14.04 üzerinde Docker 1.6.2 sürümünde çalışıyorduk. Docker'ın daha yeni sürümlerine geçmek istedik ve şu blog
yazısını gördük. [NEW APT AND YUM REPOS](https://blog.docker.com/2015/07/new-apt-and-yum-repos/#more-6860)

Bu blog yazısına göre şunları yapmamız gerekiyor:

{% highlight bash %}
$ sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D

# /etc/apt/sources.list.d/docker.list dosyasını düzenle
$ sudo vim /etc/apt/sources.list.d/docker.list
{% endhighlight %}

{% highlight text %}
# eski içeriği oradan kaldır ve aşağıdaki satırı ekle
deb https://apt.dockerproject.org/repo ubuntu-trusty main
{% endhighlight %}

{% highlight bash %}
$ sudo sudo apt-get update

$ sudo apt-get purge lxc-docker*

$ sudo apt-get install docker-engine
{% endhighlight %}

Bilgisayarlarımızı yeniden başlattığımızda Docker'ın çalışmadığını farkettik eğer siz de bunları yaptıysanız siz de
farketmişsinizdir. Sakin olun ve aşağıdaki satırı komut satırına yazın.

{% highlight bash %}
rm -rf /var/lib/docker/devicemapper
{% endhighlight %}

Şimdi bilgisayarınızı yeniden başlatıp, kaldığınız yerden devam edebilirsiniz.

Görüşmek üzere...
