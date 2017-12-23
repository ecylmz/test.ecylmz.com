---
layout: post
title: "Logwatch Bildirimlerinin SSMTP ve Yandex Mail Kullanarak Gönderilmesi"
excerpt:
categories: articles
tags: [logwatch, ssmtp, yandex]
comments: true
share: true
---

##  SSMTP Kurulumu ve Yapılandırılması

Kurulum:

{% highlight bash %}
sudo apt-get install ssmtp
{% endhighlight %}

Yandex İçin Yapılandırma:

{% highlight bash %}
sudo vim /etc/ssmtp/ssmtp.conf
{% endhighlight %}

Dosya aşağıdaki gibi olmalıdır:

{% highlight text %}
mailhub=smtp.yandex.ru:587
rewriteDomain=example.com
AuthUser=logwatch@example.com
AuthPass=parola
FromLineOverride=NO
UseTLS=YES
UseSTARTTLS=YES
{% endhighlight %}

Sistemdeki root mail kullanıcısına bu mail atanır:

{% highlight bash %}
sudo vim /etc/ssmtp/revaliases
{% endhighlight %}

Dosyaya aşağıdaki satır eklenir:

{% highlight text %}
root:logwatch@example.com:smtp.yandex.ru:587
{% endhighlight %}

##  Logwatch Yapılandırılması

{% highlight bash %}
sudo vim /usr/share/logwatch/default.conf/logwatch.conf
{% endhighlight %}

Dosyada bulunan ayarlar aşağıdakine benzer olmalıdır:

{% highlight text %}
Output = mail
Format = html
MailFrom = root
{% endhighlight %}

Geri kalan ayarları kendi seçimleriniz doğrultusunda yapabilirsiniz veya
öntanımlı ayarları olduğu gibi bırakabilirsiniz.

Test:

{% highlight bash %}
sudo logwatch --detail high --mailto mailto@example.com --range yesterday
{% endhighlight %}

