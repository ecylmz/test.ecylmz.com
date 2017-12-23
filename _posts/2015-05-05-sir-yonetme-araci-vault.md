---
layout: post
title: "Sır Yönetme Aracı: Vault"
excerpt:
categories: articles
tags: [güvenlik, yönetim]
comments: true
share: true
---

**Vault** sırlarınızı güvenli bir biçimde yönetme aracıdır. Sırdan kasıt API anahtarları, parolalar, sertifikalar ve daha
fazlasıdır. Vault bu sırlar üzerinde sıkı denetim yapan, yapılan işlemleri loglayan ve herhangi bir sırı saklamak için
yapılan birleştirilmiş bir arayüzdür.

**Vault’un ana özellikleri:**

- Güvenli sır depolama: anahtar/değer sırlarını şifrelenmiş olarak depolar. Bu depolama disk üzerine de olabilir diğer
araçlara da.
- Dinamic sırlar: AWS kullanımında otomatik sır üretimi sağlayabiliyor.
- Veri şifreleme: şifreleme ve şifre çözme işlemini yapabilmektedir. Yapılan bu şifrelemeyi SQL ortamında
kaydedebilmektedir.
- Kiralama ve yenileme: Sırlar geçerliliğini kiralama süresince saklamaktadır. Süre bittiğinde erişim hakkı da bitmektedir
ancak istemciler bu süreyi yenileme şansına sahiptir.
- İptal etme: bir sıra erişimi iptal etme veya bir kullancının sahip olduğu tüm sırları silme gibi imkanlar sağlamaktadır.


**Göze çarpan özellikler:**

Sunucu/İstemci şeklinde ve istenildiği takdirde TLS üzerinden haberleşerek çalışmaktadır.
Sisteme root olarak erişim için [Shamir's Secret Sharing](http://en.wikipedia.org/wiki/Shamir%27s_Secret_Sharing)
kullanmaktadır.

- GitHub token’ları ile sisteme giriş yapabilme ve GitHub takımları üzerinden yetkilendirme politikası hazırlanabilir.
- API mevcut.
- HashiCorp tarafından hazırlanmış Ruby istemcisi [vault-ruby](https://github.com/hashicorp/vault-ruby)
- Şifrelenmiş sırları pek çok ortamda saklama imkanı(AWS, Consul, PostgreSQL, MySQL, Transit, Generic, Özeleştirilmiş...)

## Örnek bir sır yazma ve sıra erişim:

{% highlight bash %}
$ vault write secret/hello value=world
Success! Data written to: secret/hello
{% endhighlight %}

{% highlight bash %}
$ vault read secret/hello
Key             Value
lease_id        secret/hello/d57fe039-1eef-9f4a-f3c9-63e2b29002b8
lease_duration  2592000
excited         yes
value           world
{% endhighlight %}

Kişisel görüşüm; Vault, ekosistemde kesinlikle gerekli olan bir yazılım. API, parola gibi sır paylaşımı ve depolama sorunu sunucu
sistemlerinin yönetimindeki önemli problemlerden biri. Gerek geliştiriciye verilemek istenen API anahtarı paylaşımı gerekse sunucu
sistemde kullanılan API anahtarları.

Vault kurulumu ve kullanımı ile ilgili yazıyı önümüzdeki günlerde yazmayı planlıyoruz.

Vault hakkında daha fazla bilgi için: [https://vaultproject.io/](https://vaultproject.io/)
