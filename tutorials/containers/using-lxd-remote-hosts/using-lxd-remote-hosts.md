---
id: using-lxd-remote-hosts
summary: Learn how to connect with remote LXD hosts and run containers on them.
categories: containers
tags: tutorial, desktop, server, lxd, lxc, remote-hosts, containers
difficulty: 2
status: published
feedback_url: https://github.com/canonical-websites/tutorials.ubuntu.com/issues
published: 2017-12-29
author: Marcin Mikołajczak <me@m4sk.in>

---

# Using LXD remote hosts

## Overview
Duration: 1:00

[LXD](https://linuxcontainers.org/lxd) is a container hypervisor providing a ReST API to manage LXC containers.

![logo](images/containers.png)

This tutorial will show you how to use LXD remote hosts feature to connect with remote machines and interact with them.

### Requirements

  - A computer running Ubuntu 16.04 or newer
  - Basic knowledge of LXD

positive
: For this tutorial purposes, we can use [LXD demonstration server](https://linuxcontainers.org/lxd/try-it/), so we don’t need to have two computers.

## Connecting with remote host
Duration: 3:00

### Using demonstration environment

To start demonstration environment, visit (linuxcontainers.org/lxd/try-it)[https://linuxcontainers.org/lxd/try-it/] site, read the ToS and click on the “I have read and accept the terms of service above” button. You will be able to use this session for 30 minutes.

It will come with random password that you can change using `lxc config set core.trust_password YOUR_PASSWORD` command. You can read the introduction or go through this tutorial.

Use following on local LXD to make it visible for the network:

```bash
$ lxc config set core.https_address [::]:8443
```

Use the `lxc remote add` command to add remote host on your local LXD:

```bash
$ lxc remote add tutorial 2001:470:b368:1070:216:3eff:fe9b:4a6e --password=rare
Certificate fingerprint: c6e4c891c362520159ac6073fbcd4a2b486c9426fe308a5e840d6f8558db127a
ok (y/n)? y 
Client certificate stored at server:  tryit
```

### Using real environment

If you have access to remote server running Ubuntu 16.04 or newer (or other system using LXD), you can configure to use it as remote host. On remote machine, type:

```bash
$ lxc config set core.https_address [::]:8443
$ lxc config set core.trust_password YOUR_PASSWORD
```

Type following on local machine to make it visible for the network:

```bash
$ lxc config set core.https_address [::]:8443
```

Use the `lxc remote add` command to add remote host on your local LXD:

```bash
$ lxc remote add tutorial REMOTE_IP --password=YOUR_PASSWORD
Certificate fingerprint: 21bfaf487a7c98c9fbda2df869abfd463d5382fbb8348cf7a3bf8c9cfc4b1b02
ok (y/n)?
```

### Listing remotes

We can check whether our remote host is available by using `lxc remote list` on local machine:

```bash
$ lxc remote list
+-----------------+------------------------------------------------------+---------------+--------+--------+
|      NAME       |                         URL                          |   PROTOCOL    | PUBLIC | STATIC |
+-----------------+------------------------------------------------------+---------------+--------+--------+
| images          | https://images.linuxcontainers.org                   | simplestreams | YES    | NO     |
+-----------------+------------------------------------------------------+---------------+--------+--------+
| local (default) | unix://                                              | lxd           | NO     | YES    |
+-----------------+------------------------------------------------------+---------------+--------+--------+
| tutorial        | https://[2001:470:b368:1070:216:3eff:fe9b:4a6e]:8443 | lxd           | NO     | NO     |
+-----------------+------------------------------------------------------+---------------+--------+--------+
| ubuntu          | https://cloud-images.ubuntu.com/releases             | simplestreams | YES    | YES    |
+-----------------+------------------------------------------------------+---------------+--------+--------+
| ubuntu-daily    | https://cloud-images.ubuntu.com/daily                | simplestreams | YES    | YES    |
+-----------------+------------------------------------------------------+---------------+--------+--------+
```

It worked! We can now interact with remote LXD host!

## Creating remote containers
Duration: 3:00

Creating remote containers differs from creating local containers only by the fact that we must specify our remote host, just as we do for image sources. For example, to create remote container for latest ubuntu-daily, use:

```bash
$ lxc launch ubuntu-daily: tutorial:cont1
Creating cont1
Starting cont1
```

positive
: If you have a local image on remote host and you want to use it to create container, you have to specify the remote host for image.

Now we can use remote container just like we would use local container:

```bash
$ lxc exec tutorial:cont1 exec
root@cont1:~# uname -a
Linux cont1 4.4.0-103-generic #126-Ubuntu SMP Mon Dec 4 16:23:28 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
root@cont1:~# apt-get moo
                 (__)
                 (oo)
           /------\/
          / |    ||
         *  /\---/\
            ~~   ~~
..."Have you mooed today?"...
```

## More interactions with remote containers
Duration: 4:00

Of course, LXD is very powerful tool and you can do much more things with remote hosts.

### Taking a snapshot

We can take a snapshot of remote container using `lxc snapshot` command:

```bash
$ lxc snapshot tutorial:cont1 snap1
```

### Copying containers

To create copy of remote snap, we can use `lxc copy`. We can store the copied container both on remote host and local host:

```bash
$ lxc copy tutorial:cont1 tutorial:cont2 ## store copy on remote host
$ lxc copy tutorial:cont1 local:         ## store copy locally, it is identical to `lxc copy tutorial:cont1 cont1`
```

negative
: Remember that you have to stop container before copying it. You can also take a snapshot and copy the snapshot instead source container.

### Moving containers

We use `lxc move` to copy remote container and delete it from origin. We need to stop it before doing this operation:

```bash
$ lxc stop tutorial:cont1
$ lxc move tutorial:cont1 local:
```

## That’s all
Duration: 1:00

Congrats, you made it! Now you should know how to use remote hosts to create and interact with LXD containers.

If you’d like to know more about LXD, take a look at the following resources:

* [LXD documentation in the source tree](https://github.com/lxc/lxd)
* [LXD 2.0 blog post series by Stéphane Graber](https://stgraber.org/2016/03/11/lxd-2-0-blog-post-series-012/)

Also, if you have questions or need help, you can find direct help here:

* [Linux Containers forum](https://discuss.linuxcontainers.org/)
* [Ask Ubuntu](https://askubuntu.com/)
* The `#lxcontainers` IRC channel on Freenode
