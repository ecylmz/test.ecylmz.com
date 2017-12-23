---
layout: post
title: 'MATLAB GUI ile Veri Madenciliği Kümeleme Algoritmaları'
excerpt:
categories: articles
tags: [matlab, gui, data, mining, clustering]
comments: true
share: true
---

<p>Bu projede çeşitli kümeleme algoritmalarını kullanarak, veri setlerini görsel
olarak kümelenmesi hedeflenmiştir.</p>

Kullanım kolaylığı ve hazır işlevler nedeniyle bunu MATLAB ortamında yapmayı
tercih ettim. Ortalama programlama bilgisine sahip biri, MATLAB'ı bilmese dahi
kodları rahatlıkla okuyup anlayabilir diye düşünmekteyim.

Ayrıca belirtmek isterim ki bu proje mükemmel falan değil. Paylaşmamın
tek nedeni; böyle bir şeye ihtiyacı olan ama daha önce hiç uğraşmamış olan
birisine yardımcı olabilmektir.

---

Projede Kullanılan Algoritmalar:

- Complete Link (Euclidian Mesafesi kullanılmıştır)
- Gaussian Clustering
- Fuzzy C-Means
- K-Means
- DBSCAN (k ve eps değişkenleri arayüzden değiştirme kolaylığı sağlanmıştır)

**Not:**Algoritmaların başlangıç değerleri Matlab tarafından ön tanımlı olarak
atanan değerlerdir.

**Veri Setleri:** Farklı biçimlerde ön tanımlı olarak 6 farklı veri seti sunulmuştur.
Bu verisetleri dinamik olarak üretilmektedir ve Dataset.m dosyasında tutulmaktadır.
İstenildiği takdirde kullanıcı kendi veri setini uygulamaya verebilmektedir.

**Küme Sayısı:** Bu değer ön tanımlı olarak maksimum 10 olacak şekilde ayarlanmıştır.

**Benzetim Matrisi:** Kümeleme sayısını görsel olarak görebilmeyi hedeflemektedir. Ayrıca
verinin ne kadar pürüzsüz olduğunu, düzgün dağılım gösterdiğini gösterir. Uygulamada
benzetim matrisi açık şekilde gözükmektedir.

### Uygulama Kodları

[https://github.com/ecylmz/data_mining_clustering](https://github.com/ecylmz/data_mining_clustering)

### Uygulama Ekran Görüntüsü

<figure>
        <img src="http://ecylmz.com/file/veri-madenciligi-proje.png">
        <figcaption>Veri Madenciliği Kümele Algoritmaları Projesi</figcaption>
</figure>

**Not:** Kodların %100 doğru çalıştığının garantisini veremem. :)
