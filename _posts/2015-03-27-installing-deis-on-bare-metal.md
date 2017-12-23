---
layout: post
title: Installing Deis on Bare Metal
excerpt:
categories: articles
tags: [deis, install, docker, orchestration, coreos]
comments: true
share: true
---

This post will be described how i did Deis installation from start to finish on bare metal.

3 nodes will be used in the installation. I have [VivoPC](http://www.asus.com/ASUS_VivoPC/VivoPC_VM40B/) with 8GB RAM.

## Prepare the LiveCD

Download [Ubuntu 14.04](http://www.ubuntu.com/download/desktop) and prepare the LiveCD(via unetbootin or Ubuntu startup disk creator).

## Generate SSH Key

The `deisctl` utility communicates with remote machines over an SSH tunnel. If you don’t already have an SSH key, the
following command will generate a new keypair named `deis`:

{% highlight bash %}
$ ssh-keygen -q -t rsa -f ~/.ssh/deis -N '' -C deis
{% endhighlight %}

## cloud-config.yml

Here is an example [cloud-config.yml](https://gist.github.com/c72445486fcae5f82cd2)

You must change the `ssh_authorized_keys`, `discovery` and all visible ip's.
You must also change the hostname for each node.(coreos-1, coreos-2, coreos-3)

Finally, you should copy the cloud-config.yml and [coreos_install.sh](https://gist.github.com/af42cfdab3dc5b1d7994) files to livecd's root.

## CoreOS Install

-  Start from the LiveCD.
-  Open the xterm.
-  Copy the cloud-config.yml and coreos_install.sh files to home directory.

**WARNING!!!** `coreos_install.sh` script install to `/dev/sda` disk. If you want to install the other disk, edit this script file.

{% highlight bash %}
$ cp /media/cdrom/coreos_install.sh /media/cdrom/cloud-config.yml ~
$ ./coreos-install.sh
{% endhighlight %}

This step should be repeated for each node.

## Configure DNS

See [Configure DNS](http://docs.deis.io/en/latest/managing_deis/configure-dns/#configure-dns) for more information on properly setting up your DNS records with Deis.

I used xip.io. It is so simple.

## Install Deis Platform

First check that you have deisctl installed and the version is correct.

{% highlight bash %}
$ deisctl --version
1.4.1
{% endhighlight %}

If not, follow instructions:

{% highlight bash %}
$ curl -sSL http://deis.io/deisctl/install.sh | sudo sh -s 1.4.1
$ sudo ln -fs $PWD/deisctl /usr/local/bin/deisctl
{% endhighlight %}

Find the public IP address of one of your nodes, and export it to the DEISCTL_TUNNEL environment variable (substituting your own IP address):

{% highlight bash %}
$ export DEISCTL_TUNNEL=192.168.88.50
{% endhighlight %}
If you set up the “convenience” DNS records, you can just refer to them via

{% highlight bash %}
$ export DEISCTL_TUNNEL="192.168.88.50.xip.io"
{% endhighlight %}

Before provisioning the platform, we’ll need to add the SSH key to Deis so it can connect to remote hosts during deis
run:

{% highlight bash %}
$ deisctl config platform set sshPrivateKey=~/.ssh/deis
{% endhighlight %}

We’ll also need to tell the controller which domain name we are deploying applications under:

{% highlight bash %}
$ deisctl config platform set domain=192.168.88.50.xip.io
{% endhighlight %}

Once finished, run this command to provision the Deis platform:

{% highlight bash %}
$ deisctl install platform
{% endhighlight %}

You will see output like the following, which indicates that the units required to run Deis have been loaded on the
CoreOS cluster:

{% highlight bash %}
$ deisctl install platform
{% endhighlight %}

{% highlight text %}
● ▴ ■
■ ● ▴ Installing Deis...
▴ ■ ●

Scheduling data containers...
...
Deis installed.
Please run `deisctl start platform` to boot up Deis.
{% endhighlight %}

Run this command to start the Deis platform:

{% highlight bash %}
$ deisctl start platform
{% endhighlight %}

Once you see “Deis started.”, your Deis platform is running on a cluster! You may verify that all of the Deis units are
loaded and active by running the following command:

{% highlight bash %}
$ deisctl list
{% endhighlight %}

All of the units should be active.

## Register a User

**Note:** The first user to register with Deis receives “superuser” privileges.

{% highlight bash %}
$ deis register http://192.168.88.50.xip.io:8000
username: foobar
password:
password (confirm):
email: foobar@example.com
Registered foobar
Logged in as foobar
{% endhighlight %}

## Upload Your SSH Public Key

If you plan on using git push to deploy applications to Deis, you must provide your SSH public key. Use the deis
keys:add command to upload your default SSH public key, usually one of:

{% highlight bash %}
~/.ssh/id_rsa.pub
~/.ssh/id_dsa.pub
{% endhighlight %}

{% highlight bash %}
$ deis keys:add
Found the following SSH public keys:
1) id_rsa.pub
Which would you like to use with Deis? 1
Uploading /home/foobar/.ssh/id_rsa.pub to Deis... done
Logout from a Controller
{% endhighlight %}

## Logout

{% highlight bash %}
$ deis logout
Logged out as foobar
{% endhighlight %}

## Login

{% highlight bash %}
$ deis login http://deis.example.com
username: foobar
password:
Logged in as foobar
{% endhighlight %}

References:

-   [Deis](http://docs.deis.io/en/latest/)
-   [CoreOS](https://coreos.com/docs/)
