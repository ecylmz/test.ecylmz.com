---
layout: post
title: portların açık olup olmadığının kontrolü
excerpt:
categories: articles
tags: [nc, port]
comments: true
share: true
---

server tarafında :

    nc -l -p <port no>

client tarafında :

    nc <server-ip> <port no>

eğer port kapalıysa, client tarafındaki komut verildikten sonra
tekrar komut satırına düşer. port açıksa beklemede kalır.
Bu bağlantıyı koparmak için ctrl-d yapılması gerekir.
