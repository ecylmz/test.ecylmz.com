---
layout: post
title: "MySQL-Router and MySQL Master Slave Replication with Docker"
modified:
categories: articles
excerpt:
tags: [docker, replication, mysqlrouter, highavailability]
comments: true
share: true
---

I prepared docker image for this purpose. [mysql-router](https://hub.docker.com/r/ecylmz/mysql-router/)

This image only supports the Master-Slave Replication.

For MySQL Master-Slave Replication, you must set `REPLICATION=true` environment variable before start the container.

**Disclaimer:** I'm using this image for development and testing. I have not tried yet in production environments.

### Example

[MySQL-Master-Slave](https://github.com/ecylmz/docker-mysql-router/blob/master/examples/mysql-master-slave/docker-compose.yml)

Use this docker-compose.yml file.

Clone the repo and run `docker-compose up` command in mysql-master-slave directory:

```sh
$ git clone https://github.com/ecylmz/docker-mysql-router.git
$ cd docker-mysql-router
$ docker-compose up
```

To be sure it's working:

```sh
$ docker exec -it mysqlmasterslave_mysql_slave_1_1 bash
```

**in container**

```sh
root@fd16ce4c1c92:/# mysql -uadmin -pmypass -h 172.17.0.6 -P 7002
```
This command has logged in the mysql slave. Mysql-Router use Round-Robin Algorithm.
So everytime i run this command, i expect to be in a different host.

```
mysql> SHOW VARIABLES WHERE Variable_name = 'hostname';
+---------------+--------------+
| Variable_name | Value        |
+---------------+--------------+
| hostname      | 35b65d55d6ab |
+---------------+--------------+
1 row in set (0.00 sec)
```

Quit from the container and login to MySQL with same command:

```sh
root@fd16ce4c1c92:/# mysql -uadmin -pmypass -h 172.17.0.6 -P 7002
```

```
mysql> SHOW VARIABLES WHERE Variable_name = 'hostname';
+---------------+--------------+
| Variable_name | Value        |
+---------------+--------------+
| hostname      | c12eb03b0318 |
+---------------+--------------+
1 row in set (0.00 sec)
```

Again:

```sh
root@fd16ce4c1c92:/# mysql -uadmin -pmypass -h 172.17.0.6 -P 7002
```

```
mysql> SHOW VARIABLES WHERE Variable_name = 'hostname';
+---------------+--------------+
| Variable_name | Value        |
+---------------+--------------+
| hostname      | fd16ce4c1c92 |
+---------------+--------------+
1 row in set (0.00 sec)
```

Yeah. It works!

### More Information

- [https://dev.mysql.com/doc/mysql-router/en/](https://dev.mysql.com/doc/mysql-router/en/)
- [http://mysqlhighavailability.com/easy-load-balancing-and-high-availability-using-mysql-router/](http://mysqlhighavailability.com/easy-load-balancing-and-high-availability-using-mysql-router/)
- [https://github.com/tutumcloud/mysql](https://github.com/tutumcloud/mysql)

