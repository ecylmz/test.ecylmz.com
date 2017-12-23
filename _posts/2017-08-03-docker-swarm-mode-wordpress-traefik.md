---
layout: post
title: "Docker Swarm Mode - Wordpress Traefik"
modified:
categories: articles
excerpt:
tags: [docker, swarm, wordpress, traefik, ssl]
comments: true
share: true
---

Amaç: Docker swarm mode üzerinde wordpress sitesi yayına almak.

### Docker Swarm Mode

Bu yazıyı okuyorsanız muhtemelen docker swarm mode hakkında bilginiz vardır.
Bu yazıda docker swarm'dan bahsetmeyeceğim.

Kullandığım Docker versiyonu:

```sh
ecylmz@nyx:~$ docker --version
Docker version 17.06.0-ce, build 02c1d87
```

### Traefik

Tek bir sunucu üzerinde aynı port üzerinden birden fazla web servisi sunabilmek için reverse proxy
aracına ihtiyacımız var. [Traefik](https://traefik.io/) Go programlama dili
yazılmış kullanımı kolay güzel bir araç. LetsEncrypt desteği de olduğundan dolayı tercih ettim.

### Wordpress

Wordpress için resmi [docker imajını](https://hub.docker.com/_/wordpress/) kullandım.

### Traefik konuşlandırma

traefik'i docker-compose.yml dosyası ile konuşlandırdım.

```sh
mkdir traefik && cd traefik
touch acme.json
```

Aşağıdaki docker-compose.yml ve traefik.toml dosyaları traefik dizini içerisine
kaydedildi.

```yml
# docker-compose.yml
version: '3.3'

services:
  traefik:
    image: traefik
    command: -c /etc/traefik.toml
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /home/ecylmz/traefik/acme.json:/etc/acme.json
    networks:
      - "traefik-net"
    configs:
      - source: traefik_config
        target: /etc/traefik.toml

configs:
  traefik_config:
    file: ./traefik.toml

networks:
  traefik-net:
    external:
      name: "traefik-net"
```

```toml
# traefik.toml

# defaultEntryPoints must be at the top because it should not be in any table below
defaultEntryPoints = ["http", "https"]

[web]
# Port for the status page
address = ":8080"

# Entrypoints, http and https
[entryPoints]

# http should be redirected to https
[entryPoints.http]
address = ":80"
[entryPoints.http.redirect]
entryPoint = "https"

# https is the default
[entryPoints.https]
address = ":443"
[entryPoints.https.tls]

# Enable ACME (Let's Encrypt): automatic SSL
[acme]
email = "emrecan@ecylmz.com"
storage = "/etc/acme.json"
entryPoint = "https"
onDemand = true
OnHostRule = true

[docker]
endpoint = "unix:///var/run/docker.sock"
domain = "ecylmz.com"
swarmmode = true
watch = true
exposedbydefault = false

[[acme.domains]]
main = "test.ecylmz.com"
```

```sh
$ docker stack deploy --compose-file docker-compose.yml traefik
```

Servisin çalışıp çalışmadığını aşağıdaki komutla kontrol edebilirsin:

```sh
docker service ps traefik_traefik
```

`traefik.toml` dosyasının sonundaki satıra dikkat ederseniz domain olarak
"test.ecylmz.com" girdim. Traefik bu domain için LetsEncrypt kullanarak otomatik
SSL üretimi yapacak.

Ayrıca öntanımlı olarak tüm istekler 443'e yönlendirilmiş durumda.

### Wordpress konuşlandırma

Gerekli ortamı hazırlayalım:

```sh
mkdir wordpress && cd wordpress
mkdir wp_data db_data

# mysql root parolası üretilir
openssl rand -base64 20 > db_root_password.txt

# wordpress veritabanı için parola üretilir
openssl rand -base64 20 > db_password.txt
```

```yml
# docker-compose.yml

version: '3.1'

services:
   db:
     image: mysql:latest
     volumes:
       - /home/ecylmz/wordpress/db_data:/var/lib/mysql
     environment:
       MYSQL_ROOT_PASSWORD_FILE: /run/secrets/db_root_password
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD_FILE: /run/secrets/db_password
     secrets:
       - db_root_password
       - db_password
     networks:
       - backend

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     volumes:
       - /home/ecylmz/wordpress/wp_data:/var/www/html
     networks:
       - "traefik-net"
       - backend
     deploy:
       labels:
         traefik.frontend.rule: "Host:test.ecylmz.com"
         traefik.port: "80"
         traefik.enable: "true"
         traefik.backend: "wordpress"
         traefik.docker.network: "traefik-net"
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD_FILE: /run/secrets/db_password
     secrets:
       - db_password

secrets:
   db_password:
     file: db_password.txt
   db_root_password:
     file: db_root_password.txt

volumes:
    db_data:
      driver: local
    wp_data:
      driver: local
networks:
  traefik-net:
    external: true
  backend:
```

`docker-compose.yml` dosyasında dikkat etmeniz gereken noktalar; networks ve
deploy altındaki etiketler. traefik ile etkileşimi bu etiketler aracılığıyla
oluyor. Örneğin: `traefik.frontend.rule` etiketi tanımlanmasaydı, traefik
wordpress'e gerekli yönlendirmeyi yapamayacaktı.

```sh
docker stack deploy --compose-file docker-compose.yml wp
```

Servisin durumunu görmek için:

```sh
docker service ps wp_wordpress
docker service ps wp_db
```

### DNS yapılandırması

Bu örnek için test.ecylmz.com adresi için A kaydı ekledim.

### Test

web tarayıcıdan domaine girilip wordpress kurulumu tamamlanır.

### Kapanış konuşması

Bu örnek tek bir sunucu üzerinde yapılan kurulumdan bahsetmektedir. Eğer
wordpress'i ölçeklemek isterseniz `docker-compose.yml` dosyasında ayarlanan
volume'ler diğer sunuculardan da erişilebilir olmalıdır.

Çoğu kişiyi ürküten kısım da burasıdır. Pek çok volume driver'ı var ancak
production ortamda driver problemi yaşama ihtimali çok ürkütücüdür. Glusterfs
Ceph gibi yazılımlar kullanılabilir ama insan yine de tedirgin oluyor.

Söyleyeceklerim bu kadar.

### Kaynaklar

1. [https://docs.docker.com/engine/swarm/secrets/#advanced-example-use-secrets-with-a-wordpress-service](https://docs.docker.com/engine/swarm/secrets/#advanced-example-use-secrets-with-a-wordpress-service)
2. [https://docs.traefik.io/](https://docs.traefik.io/)
