---
layout: post
title: "Vault Komutları"
excerpt:
categories: articles
tags: [vault, guvenlik]
modified:
comments: true
share: true
---

## Kurulum

[https://vaultproject.io/downloads.html](https://vaultproject.io/downloads.html) sayfasından ilgili Vault projesi
indirilip arşiv içerisindeki ikili dosya çalıştırılabilir `PATH` dizinine konur.

Kurulum doğru olup olmadığını doğrulamak için komut satırına aşağıdaki komut yazılır

{% highlight bash %}
$ vault
{% endhighlight %}

## Vault Sunucusunu Başlat

Production sunucuda kullanacaksanız kendi dokümanına göre TLS'i aktif edin.

{% highlight bash %}
$ cat config.hcl
backend "file" {
	path = "vault"
}

listener "tcp" {
	address = "127.0.0.1:8200"
	tls_disable = 1
}
{% endhighlight %}

{% highlight bash %}
$ vault server -config=config.hcl
{% endhighlight %}

## Vault Sunucusu Adresini Belirt

{% highlight bash %}
$ export VAULT_ADDR='http://127.0.0.1:8200'
{% endhighlight %}

## Yapılan İşlemleri Kayıt Altına Al

{% highlight bash %}
$ vault audit-enable file path=~/vault/vault_audit.log
{% endhighlight %}

##  Sır İşlemleri

### Sır Yazma

- Güvenli olmayan şekilde:

{% highlight bash %}
$ vault write secret/hello value=world excited=yes
Success! Data written to: secret/hello
{% endhighlight %}

- Dosyadan okuyarak yazma:

{% highlight bash %}
$ cat data.json
{ "value": "itsasecret" }

$ vault write secret/password @data.json
{% endhighlight %}

### Sır Okuma

{% highlight bash %}
$ vault read secret/hello
Key             Value
lease_id        secret/hello/d57fe039-1eef-9f4a-f3c9-63e2b29002b8
lease_duration  2592000
excited         yes
value           world
{% endhighlight %}

### Sır Silme

{% highlight bash %}
$ vault delete secret/hello
Success! Deleted 'secret/hello'
{% endhighlight %}

### Bölümleri Listele

{% highlight bash %}
$ vault mounts
{% endhighlight %}

##  Bölümler

### Bölüm Ekle

{% highlight bash %}
$ vault mount -path=bolum_adi generic
{% endhighlight %}

### Bölüm Sil

{% highlight bash %}
$ vault unmount generic/
Successfully unmounted 'generic/'!
{% endhighlight %}

## Kimlik Doğrulama

### Token ile Kimlik Doğrulama

- Token oluşturma

{% highlight bash %}
$ vault token-create -policy=root
6c38f603-6441-2161-c543-ee15b7206563
{% endhighlight %}

- Token silme

{% highlight bash %}
$ vault token-revoke 6c38f603-6441-2161-c543-ee15b7206563
Revocation successful.
{% endhighlight %}

- Token ile kimlik doğrula

{% highlight bash %}
$ vault auth d08e2bd5-ffb0-440d-6486-b8f650ec8c0c
Successfully authenticated! The policies that are associated
with this token are listed below:

root
{% endhighlight %}

### GitHub ile Kimlik Doğrulama

- GitHub'ı etkinleştir

{% highlight bash %}
$ vault auth-enable github
Successfully enabled 'github' at 'github'!
{% endhighlight %}

- İstenilen GitHub organizasyonunu sisteme ekle, `owners` takımını root politikası hakları ile yetkilendir.

{% highlight bash %}
$ vault write auth/github/config organization=pivotus
Success! Data written to: auth/github/config

$ vault write auth/github/map/teams/owners value=root
Success! Data written to: auth/github/map/teams/owners
{% endhighlight %}


- GitHub token ile kimlik doğrulama

Token üretmek için: [Kişisel erişim token'ı](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)

{% highlight bash %}
$ vault auth -method=github token=e6919b17dd654f2b64e67b6369d61cddc0bcc7d5
Successfully authenticated! The policies that are associated
with this token are listed below:

root
{% endhighlight %}

- GitHub tokenlarını sil

{% highlight bash %}
$ vault token-revoke -mode=path auth/github
{% endhighlight %}

- GitHub yetkilendirmeyi devre dışı bırak

{% highlight bash %}
$ vault auth-disable github
Disabled auth provider at path 'github'!
{% endhighlight %}

##  Erişim Kontrol Politikaları

### Örnek Politika Formatı

`acl.hcl:`
{% highlight text %}
path "sys" {
	  policy = "deny"
}

path "secret" {
	  policy = "write"
}

path "secret/foo" {
	  policy = "read"
}
{% endhighlight %}

### Politika Yazma

Secret isminda politika oluşturmak için:

{% highlight bash %}
$ vault policy-write secret acl.hcl
Policy 'secret' written.
{% endhighlight %}

### Politika Testi

{% highlight bash %}
$ vault token-create -policy="secret"
d97ef000-48cf-45d9-1907-3ea6ce298a29

$ vault auth d97ef000-48cf-45d9-1907-3ea6ce298a29
Successfully authenticated! The policies that are associated
with this token are listed below:

secret
{% endhighlight %}

{% highlight bash %}
$ vault write secret/bar value=yes
Success! Data written to: secret/bar

$ vault write secret/foo value=yes
Error writing data to secret/foo: Error making API request.

URL: PUT http://127.0.0.1:8200/v1/secret/foo
Code: 400. Errors:

* permission denied
{% endhighlight %}

### GitHub Takımına Politika Tanımla

{% highlight bash %}
$ vault write auth/github/map/teams/foobar value=secret
Success! Data written to: auth/github/map/teams/foobar
{% endhighlight %}

## Referans

Daha detaylı anlatım için [tıklayın](https://vaultproject.io/docs/index.html)

