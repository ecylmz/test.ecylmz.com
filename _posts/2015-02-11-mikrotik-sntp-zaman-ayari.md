---
layout: post
title: "Mikrotik'te SNTP Zaman Ayarı"
excerpt:
categories: articles
tags: [mikrotik, sntp]
comments: true
share: true
---

Mikrotik üzerinde el ile yapılan zaman düzenlemeleri, bu cihazın yeniden başlaması durumunda tekrardan 1/1/1970 zamanına dönmektedir.
Bu durumun nedeni, Mikrotik RouterOS cihazlarında CMOS pilinin bulunmamasıdır.
Bu durumla başa çıkabilmek için saat ayarını el ile değil, SNTP üzerinden otomatik olarak kendisinin yapmasını sağlamaktır.

### Adımlar

-   Yönetim panelindeki sol menüden **"System > SNTP Client"** menüsüne girilir.
-   Gelen sayfadaki alanlar şu şekilde doldurulur:

    **Mode**: unicast

    **Primary NTP Server**: 131.211.8.244
-   **"Apply"** butonuna tıklanır.

**Not**: Yukarıdaki NTP sunucusunun adresi: [http://www.pool.ntp.org/zone/tr](http://www.pool.ntp.org/zone/tr) bağlantısından alınmıştır.
