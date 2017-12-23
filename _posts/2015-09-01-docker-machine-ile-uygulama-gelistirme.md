---
layout: post
title: Docker Machine ile Uygulama Geliştirme
excerpt:
categories: articles
tags: [docker, machine]
comments: true
share: true
---

## Neden "Docker Machine"

Docker Machine, kişisel bilgisayarınızda ve bir çok bulut sağlayacılarında size otomatik olarak Docker ortamı hazırlar.
Ayrıca hazırlanan bu ortamları basit komutlarla yönetmenizi sağlar.

Eğer bir de benim gibi bazı şeylere takıntılıysanız (bilgisayarınızı/sunucunuzu temiz tutmak gibi) bu aracı seveceğine
eminim.

Bu yazıda kişisel bilgisayarımda kurulum yapacağım. Docker Machine VirtualBox sürücülerini kullanarak bir sanal makine
oluşturuyor. Oluşturulan bu makine `boot2docker` işletim sistemini barındırmaktadır.


## Hazırlık

-  [Docker kurulumu](https://docs.docker.com/installation/ubuntulinux/)
-  [VirtualBox kurulumu](https://www.virtualbox.org/wiki/Linux_Downloads)
-  [Docker Machine kurulumu](https://docs.docker.com/machine/install-machine/)
-  [Docker Compose kurulumu](https://docs.docker.com/compose/install/)

## docker-machine ile sanal makine oluşturma

{% highlight bash %}
$ docker-machine create --native-ssh web --driver virtualbox
{% endhighlight %}

<figure>
	<img src="http://ecylmz.com/file/docker-machine.png">
	<figcaption>Docker Machine Ayarlamaları</figcaption>
</figure>

<figure>
	<img src="http://ecylmz.com/file/docker-machine-help.png">
	<figcaption>Docker Machine Komutları</figcaption>
</figure>

**Öneriler:**
Docker ile bir uygulama çalıştırdığınızda artık "localhost:3000" adresine değil de yukarıda yazan ip'ye bağlanmanız
gerekecek. Bu ip'yi `/etc/hosts` dosyanıza yazabilirsiniz.

Yukarıdaki kurulumları ve ayarlamaları yaptıysanız artık uygulama geliştirme işlemine geçebilirsiniz.
Önceki yazılarımdan olan [Docker Compose ile Rails Uygulama Geliştirme](http://ecylmz.com/articles/docker-compose-ile-rails-uygulama-gelistirme/) başlıklı yazıma gözatabilirsiniz.

GitHub'da bulunan [#878](https://github.com/docker/machine/issues/878) nolu issue'yu takipe etmenizi öneririm.

Makine oluştururken --native-ssh argümanı verilmezse verdiğim linkteki sıkıntılar ortaya çıkmaktadır.

---

Bu yazıdaki amacım kurulumların nasıl yapılacağını göstermek değildi. Kurulum sayfaları zaten yeterince anlaşılır ve
minimum ingilizce ile halledilebilir düzeyde. Amacım Docker Machine'in aslında aradığınız bir araç olabileceğini sizlere
göstermek ve benim gibi bu tip araçları görünce heyecanlananları biraz daha heyecanlandırmak. :)
