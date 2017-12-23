---
layout: post
title: "Mikrotik'te Tüm Trafiği Loglama"
excerpt:
categories: articles
tags: [mikrotik, rsyslog, remote, logging]
comments: true
share: true
---

##  Mikrotik Yapılandırması

**Tüm işlemler Mikrotik web arayüzünden gerçekleştirilmektedir.**

Web arayüzüne giriş yapıldıktan sonra **IP > Firewall > Filter Rules > Add New** penceresine gelinir. Burada tüm trafiği izlemek için firewall kuralları
tanımlanır. Gelen pencerede sadece aşağıdaki alanlar doldurulur:

**Kural 1**: Bağlantı kurma girişimlerini logla.

**Enabled**: İşaretli  
**Protocol**: 6 (tcp)  
**TCP Flags**: syn  
**Action**: log  
**Log**: İşaretli  
**Log Prefix**: mikrotik

---

**Kural 2**: Bağlantı sonlandırma girişimlerini logla.

**Enabled**: İşaretli  
**Protocol**: 6 (tcp)  
**TCP Flags**: fin  
**Action**: log  
**Log**: İşaretli  
**Log Prefix**: mikrotik


##  Uzağa Loglama

Bu logları rsyslog ile loglamak için [Mikrotik'te Rsyslog ile Uzağa
Loglama](http://ecylmz.com/articles/mikrotikte-rsyslog-ile-uzaga-loglama/)
başlıklı yazıyı uygulayın.
