---
layout: post
title: "Docker Compose ile MySQL Master Slave Kurulumu"
modified:
categories: articles
excerpt:
tags: [docker, docker-compose, mysql, replication]
comments: true
share: true
---

Bu yapıyı kurmak için ihtiyacınız olan imajı [Tutum](https://www.tutum.co/) sunmaktadır.
Yok ben imajları kendim hazırlamak istiyorum derseniz, sizi şuraya alalım:

[https://github.com/docker-library/mysql](https://github.com/docker-library/mysql)

Biz burada Tutum'un hazırladığı imajla devam edeceğiz:

[https://github.com/tutumcloud/mysql](https://github.com/tutumcloud/mysql)

**Not:** Aşağıda uygulanan adımlar geliştirme/öğrenme amaçlıdır. Sunucu ortamında denemeyiniz.

### docker-compose.yml

Hali hazırda bir docker-compose.yml dosyanız varsa onu kullanabilirsiniz.
Eğer yoksa, proje dizininde bu isimde dosya oluşturup, içerisine aşağıdaki satırları yazalım.

{% highlight bash %}
$ # ben yeni bir proje açıyorum.
$ mkdir mysql-master-slave && cd mysql-master-slave
$ vim docker-compose.yml
{% endhighlight %}

{% highlight text %}
mysql_master:
  image: tutum/mysql:5.6
  environment:
    MYSQL_USER: 'admin' # default
    MYSQL_PASS: 'mypass'
    REPLICATION_MASTER: 'true'
    REPLICATION_USER: 'replica' # default
    REPLICATION_PASS: 'replica' # default
  ports:
    - '3306:3306'

mysql_slave:
  image: tutum/mysql:5.6
  environment:
    MYSQL_PASS: 'mypass'
    REPLICATION_SLAVE: 'true'
  ports:
    - '3307:3306'
  links:
    - mysql_master:mysql
{% endhighlight %}


### Çalıştırma

Aşağıdaki komut öncelikle tutum/mysql:5.6 imajını indirecektir. Bu adım bağlantı hızınıza bağlı olarak bir miktar
sürenizi alabilir.

{% highlight bash %}
$ docker-compose up
{% endhighlight %}

### Test

Örnek bir veritabanı ve tablo oluşturup içerisine veri yazarak yapımızı test edelim.

- Yeni bir terminal açıp, `master` konteynerimize girelim:

Konteyner içerisine girmemiz için konteyner ismini bilmemiz gerekiyor.
Bu yazı için konteyner ismi: `mysqlmasterslave_mysql_master_1` eğer siz farklı dizinde
`docker-compose up` dediyseniz konteyner isminiz de farklı olacaktır.

Konteyner isminizi öğrenmek için aşağıdaki komutu uygulayın:

{% highlight bash %}
$ docker-compose ps
{% endhighlight %}

Konteynerimize girelim:

{% highlight bash %}
$ docker exec -it mysqlmasterslave_mysql_master_1 bash

root@a8556bbb43cb:/#
{% endhighlight %}

- Artık konteyner içindeyiz. Şimdi MySQL'e bağlanalım:

{% highlight bash %}
$ mysql -uadmin -pmypass

Warning: Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 5.6.28-0ubuntu0.14.04.1-log (Ubuntu)

Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
{% endhighlight %}

- `example` adında veritabanı oluşturalım:

{% highlight text %}
mysql> CREATE DATABASE example;
Query OK, 1 row affected (0.00 sec)

mysql> USE example;
Database changed
{% endhighlight %}

- `potluck` adında tablo oluşturalım:

{% highlight text %}
mysql> CREATE TABLE potluck (id INT NOT NULL PRIMARY KEY AUTO_INCREMENT,
name VARCHAR(20),
food VARCHAR(30),
confirmed CHAR(1),
signup_date DATE);

Query OK, 0 rows affected (0.07 sec)
{% endhighlight %}

- Örnek veriler girelim:

{% highlight text %}
mysql> INSERT INTO `potluck` (`id`,`name`,`food`,`confirmed`,`signup_date`) VALUES (NULL, "John", "Casserole","Y", '2012-04-11');
Query OK, 1 row affected (0.03 sec)

mysql> INSERT INTO `potluck` (`id`,`name`,`food`,`confirmed`,`signup_date`) VALUES (NULL, "Sandy", "Key Lime Tarts","N", '2012-04-14');
Query OK, 1 row affected (0.03 sec)
{% endhighlight %}

- Tablomuza bir göz atalım bakalım neler var:

{% highlight text %}
mysql> SELECT * FROM potluck;
+----+-------+----------------+-----------+-------------+
| id | name  | food           | confirmed | signup_date |
+----+-------+----------------+-----------+-------------+
|  1 | John  | Casserole      | Y         | 2012-04-11  |
|  2 | Sandy | Key Lime Tarts | N         | 2012-04-14  |
+----+-------+----------------+-----------+-------------+
2 rows in set (0.00 sec)
{% endhighlight %}

- Yeni bir terminal açıp `slave` konteynerimize bağlanalım:

{% highlight bash %}
$ docker exec -it mysqlmasterslave_mysql_slave_1 bash

root@c38dd92a337b:/#
{% endhighlight %}

- MySQL'e bağlanalım:

{% highlight bash %}
$ mysql -uadmin -pmypass
Warning: Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 5.6.28-0ubuntu0.14.04.1-log (Ubuntu)

Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
{% endhighlight %}

- Bakalım example isimli veritabanı burada oluşmuş mu?

{% highlight text %}
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| example            |
| mysql              |
| performance_schema |
| test               |
+--------------------+
5 rows in set (0.00 sec)
{% endhighlight %}

- Veritabanının oluştuğunu gördüğümüze göre artık `potluck` isimli tablo içeriğini görelim:

{% highlight text %}
mysql> USE example;
Database changed

mysql> SELECT * FROM potluck;
+----+-------+----------------+-----------+-------------+
| id | name  | food           | confirmed | signup_date |
+----+-------+----------------+-----------+-------------+
|  1 | John  | Casserole      | Y         | 2012-04-11  |
|  2 | Sandy | Key Lime Tarts | N         | 2012-04-14  |
+----+-------+----------------+-----------+-------------+
2 rows in set (0.00 sec)
{% endhighlight %}

### Sonuç

Görüldüğü üzere başarılı bir MySQL Master Slave yapısı kurmuş olduk.

### Daha fazlası için

- [https://github.com/tutumcloud/mysql](https://github.com/tutumcloud/mysql)
- [https://www.digitalocean.com/community/tutorials/a-basic-mysql-tutorial](https://www.digitalocean.com/community/tutorials/a-basic-mysql-tutorial)
