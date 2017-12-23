---
layout: post
title: Tekir Ticari Otomasyon İçin Güvenlik Önlemleri
excerpt:
categories: articles
tags: [tekir, muhasebe, ticari, otomasyon, jboss, güvenlik]
comments: true
share: true
---

Merhaba,

Muhasebe işleri için [Tekir](http://www.tekir.com.tr/)  kullanıyorsanız ve kurulumunu resmi sayfasından yayınlanan
[dokümandan](http://tekir.com.tr/sites/default/files/linux-kurulum-2.1.pdf) yaptıysanız, sistemde çok büyük açıklarla
yaşıyorsunuz demektir. Belki de sunucunuzda at koşturan birileri vardır. Kurulumu sunucu ortamında değil de yerelde
yaptıysanız riskiniz daha da azdır doğal olarak. Ama yine de ağınıza bağlanan birileri bu açıktan faydalanabilir. Yok
benim etrafımda bunu yapabilecek kimse yok derseniz ne ala.


##  Ne gibi problemler var?

- CVSS skoru 10 - [Apache Tomcat / JBoss EJBInvokerServlet / JMXInvokerServlet Marshalled Object Remote Code Execution](http://seclists.org/bugtraq/2013/Dec/att-133/ESA-2013-094.txt)
- jmx konsolu ve web konsolu aktif. Eğer sadece tekir için kurulum yapıyorsanız bu konsollara ihtiyacınız da olmayacaktır.


##  Yapılması gerekenler

Yapılması gereken sorunlu kısımları silmek. Ben aşağıdaki adımları Debian sunucu için yaptım. Siz hangi işeltim sistemini kullanıyorsanız
ona uygun işlemler yapın.

{% highlight bash %}
# ilgili dizine geç
cd /opt/jboss-4.2.3.GA/server/default/deploy

# güvenlik açığı çıkaran dosyaları sil
sudo rm -rf http-invoker.sar jmx-console.war jmx-invoker-service.xml management/console-mgr.sar

# jboss servisini yeniden başlat
sudo service jboss4.sh restart
{% endhighlight %}

Eğer bu açıklarla bir süre yaşadıysanız sunucunuzu kontrol edin derim.
