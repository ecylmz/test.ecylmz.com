---
layout: post
title: "Kullanıcı Yük Testi Aracı: Locust"
excerpt:
categories: articles
tags: [testing]
comments: true
share: true
---

[Locust](http://locust.io/), kolayca kullanılabilen bir kullanıcı yük testi aracıdır. Web sitelerine yapılacak olan yük testleri ve eş
zamanlı olarak kaç kullanıcıya hizmet verebileceğini saptamak için kullanılan bir araçtır.

Test sırasında bu yazılım web sitenize saldırı yapacaktır. Yapılan saldırı ile ilgili verileri size bir arayüzden
sunmaktadır. Böylece sisteminizi gerçek kullanıcılara açmadan önce kodunuzdaki veya sunucunuzdaki darboğazları bulma
imkanınız olacaktır.

Test senaryolarını locustfile denilen formatta Python programlama diliyle yazıldığından, geliştiriciler için de büyük
kolaylık sağlamaktadır.

### Kurulum

{% highlight bash %}
pip install locustio
{% endhighlight %}

veya

{% highlight bash %}
easy_install locustio
{% endhighlight %}

### Örnek Bir Locustfile

{% highlight python %}
from locust import HttpLocust, TaskSet

def login(l):
    l.client.post("/login", {"username":"ellen_key", "password":"education"})

def index(l):
    l.client.get("/")

def profile(l):
    l.client.get("/profile")

class UserBehavior(TaskSet):
    tasks = {index:2, profile:1}

    def on_start(self):
        login(self)

class WebsiteUser(HttpLocust):
    task_set = UserBehavior
    min_wait=5000
    max_wait=9000
{% endhighlight %}


### Çalıştır

{% highlight bash %}
locust -f ../locust_files/my_locust_file.py --host=http://example.com
{% endhighlight %}

Daha detaylı örnekler ve kullanım için: [http://docs.locust.io/](http://docs.locust.io/)
