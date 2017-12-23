---
layout: post
title: Docker Compose ile Rails Uygulama Geliştirme
excerpt:
categories: articles
tags: [docker, compose, rails, postgresql]
comments: true
share: true
---

Bu yazıda Docker Compose ile Rails/PostgreSQL uygulama geliştirmesi yapılacaktır.

##  Docker Compose Kurulumu

{% highlight bash %}
$ curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
$ chmod +x /usr/local/bin/docker-compose
{% endhighlight %}

##  Uygulama için Dockerfile

Yeni bir çalışma dizini oluşturulur ve `Dockerfile` aşağıdaki gibi olmalıdır:

{% highlight bash %}
$ mkdir hello; cd hello
$ vim Dockerfile
{% endhighlight %}

{% highlight text %}
FROM ruby:2.2.0
RUN apt-get update -qq && apt-get install -y build-essential libpq-dev
RUN mkdir /app
WORKDIR /app
ADD Gemfile /app/Gemfile
RUN bundle install
ADD . /app
{% endhighlight %}

##  Gemfile

Uygulamayı oluşturabilmek için gerekli minimum `Gemfile` aşağıdaki gibidir:

{% highlight text %}
source 'https://rubygems.org'
gem 'rails', '4.2.0'
{% endhighlight %}

##  docker-compose.yml

Sonunda büyünün gerçekleştiği yerdeyiz. `docker-compose.yml` dosyası aşağıdaki
gibidir:

{% highlight text %}
db:
  image: postgres
  ports:
    - "5432"

web:
  build: .
  command: bundle exec rails s -p 3000 -b '0.0.0.0'
  volumes:
    - .:/app
  ports:
    - "3000:3000"
  links:
    - db
{% endhighlight %}

##  Rails Projesini Oluşturma

{% highlight bash %}
$ docker-compose run web rails new . --force --database=postgresql --skip-bundle
{% endhighlight %}

Oluşan `Gemfile` içerisindeki `therubyracer` gem'ini aktif hale getirin:

{% highlight text %}
gem 'therubyracer', platforms: :ruby
{% endhighlight %}

##  İmajları Üret

{% highlight bash %}
$ docker-compose build
{% endhighlight %}

##  Veritabanını Ayarla

`config/database.yml` dosyasında ilgili yerler aşağıdaki şekilde değiştirilir.

{% highlight yaml %}
default: &default
  adapter: postgresql
  encoding: unicode
  pool: 5

development:
  <<: *default
  database: app_development
  username: postgres
  password:
  host: db

test:
  <<: *default
    database: app_test
{% endhighlight %}

##  Uygulamayı Çalıştır

{% highlight bash %}
$ docker-compose up
{% endhighlight %}

{% highlight text %}
web_1 | [2015-03-04 17:16:29] INFO  WEBrick 1.3.1
web_1 | [2015-03-04 17:16:29] INFO  ruby 2.2.0 (2014-12-25) [x86_64-linux-gnu]
web_1 | [2015-03-04 17:16:29] INFO  WEBrick::HTTPServer#start: pid=1 port=3000
{% endhighlight %}

##  Veritabanını Oluştur

{% highlight bash %}
$ docker-compose run web rake db:create
{% endhighlight %}
