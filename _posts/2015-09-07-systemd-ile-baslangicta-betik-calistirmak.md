---
layout: post
title: "Systemd ile Başlangıçta Betik Çalıştırmak"
modified:
categories: articles
excerpt:
tags: [systemd, upstart, scripts]
comments: true
share: true
---

Amacınız basit olarak bir betiği başlangıçta çalıştırmak ve onu start/stop/restart gibi komutlarla kolayca yönetmek
istiyorsanız aşağıdaki bilgiler işinize yarayabilir.

Örnek betik: **/opt/bar/foo.rb**

{% highlight bash %}
$ vim /opt/bar/foo.rb
{% endhighlight %}


{% highlight ruby %}
while true
  `touch /tmp/foo`
  sleep 5
end
{% endhighlight %}

- İlk olarak betik için servis dosyası açılır ve içerisi aşağıdaki gibi doldurulur:

{% highlight bash %}
$ vim /lib/systemd/system/foo.service
{% endhighlight %}

{% highlight text %}
[Unit]
Description=foo
After=network.target

[Service]
WorkingDirectory=/opt/bar/foo.rb
ExecStart=/usr/bin/ruby /opt/bar/foo.rb

[Install]
WantedBy=multi-user.target
{% endhighlight %}

Yukarıdaki `After=network.target` network aktif olduktan sonra bu betik çalışmaya başlasın demektir.
Diğer parametrelerin de değişken isimlerinden ne yaptığı anlaşılabilir.

- Başlangıçta çalışması için aşağıdaki komut verilir:

{% highlight bash %}
$ systemctl enable foo.service
{% endhighlight %}

- Mevcut oturumda çalıştırmak için:

{% highlight bash %}
$ systemctl start foo.service
{% endhighlight %}

Betik ayrıca `service` komutuyla da yönetilebilir.

{% highlight bash %}
$ service foo [start|stop|restart]
{% endhighlight %}

### Daha fazlası için

- [How To Use Systemctl to Manage Systemd Services and Units](https://www.digitalocean.com/community/tutorials/how-to-use-systemctl-to-manage-systemd-services-and-units)
- [Understanding Systemd Units and Unit Files](https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files)
- [Systemd Essentials: Working with Services, Units, and the Journal](https://www.digitalocean.com/community/tutorials/systemd-essentials-working-with-services-units-and-the-journal)
