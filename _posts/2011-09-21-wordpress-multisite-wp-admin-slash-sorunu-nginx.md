---
layout: post
title: wordpress multisite wp-admin slash sorunu (nginx)
excerpt:
categories: articles
tags: [wordpress, multisite, wp-admin, nginx]
comments: true
share: true
---

Multsite bir wordpress'e sahipseniz. alt sitelerin admin panellerine girerken:

	example.com/site/wp-admin/

yazmak zorunda kalıyorduk. Sondaki slash'ı kaldırınca:

	example.com/wp-admin

linkine düşüyordu URL.

`nginx` yapılandırmanıza şu satırı eklemeniz problemi çözecektir.

	rewrite ^/[_0-9a-zA-Z-]+.*(/wp-admin)$ $scheme://$host$uri/ permanent;


