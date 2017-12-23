---
layout: post
title: google app engine ile gdata kullanımı
excerpt:
categories: articles
tags: [google, appengine]
comments: true
share: true
---

indirilen gdata paketinin içinde, src dizinin altındaki `atom` ve `gdata`
klasörlerini app engine uygulamanızın ana dizinine kopyalamak yeterli olacaktır.

betikte bunları çağırmak içinse :
örneğin takvim hizmetine ulaşmak istiyoruz.

`import gdata.calendar` demek yeterli olacaktır...
