---
layout: post
title: "Mikrotik'te Rsyslog ile Uzağa Loglama"
excerpt:
categories: articles
tags: [mikrotik, rsyslog, remote, logging]
comments: true
share: true
---

##  Rsyslog Yapılandırması

Açılan dosyada aşağıdaki satırlar aktif hale getirilir. Böylece logların UDP ile 514. port üzerinden rsyslog'a ulaşabilme imkanı sağlanır.

{% highlight bash %}
$ vim /etc/rsyslog.conf
{% endhighlight %}

{% highlight text %}
$ModLoad imudp
$UDPServerRun 514
{% endhighlight %}

Logun geleceği ip tanımlanır.

{% highlight bash %}
$ vim /etc/rsyslog.d/50-mikrotik.conf
{% endhighlight %}

{% highlight text %}
:fromhost-ip,isequal,"192.168.88.1" /var/log/mikrotik.log
& ~
{% endhighlight %}

Logrotate ile logları günlük olarak bölme işlemi yapılır.

{% highlight bash %}
$ vim /etc/logrotate.d/mikrotik
{% endhighlight %}

Alttaki satırlar eklenir:

{% highlight text %}
/var/log/mikrotik.log {
    daily
    rotate 365
    missingok
    notifempty
    delaycompress
    compress
    sharedscripts
    postrotate
    if [ -f /var/run/rsyslogd.pid ]; then
        service rsyslog restart > /dev/null
    fi
    endscript
}
{% endhighlight %}

Log sunucusu yeniden başlatılır.

{% highlight bash %}
$ sudo service rsyslog restart
{% endhighlight %}

Logları canlı izlemek için:

{% highlight bash %}
$ sudo tail -f /var/log/mikrotik.log
{% endhighlight %}

##  Mikrotik Yapılandırması

**Tüm işlemler Mikrotik web arayüzünden gerçekleştirilmektedir.**

`System > Logging > Actions` menüsünden `remote`'a tıklanır.

Gelen pencere şöyle doldurulur:

**Remote Address**: 192.168.x.x  
**Remote Port**: 514  
**BSD Syslog**: İşaretli  
**Syslog Facility**: 3 (daemon)


`System > Logging > Rules > Add New` butonuna tıklanır.

Gelen pencere şöyle doldurulur:

**Enabled**: İşaretli  
**Topics**: !debug  
**Prefix**: mikrotik  
**Action**: remote
