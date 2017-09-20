---
layout: post
title:  "FAF in container"
date:   2017-09-20 10:00:00 +0100
author: marusak
visible: 1
categories: FAF container
---
FAF is a framework for aggregating and analyzing reports of crashes and has never
been easier to deploy it.

# Why deploying own FAF
Firstly, standard users probably do not want to deploy own FAF - FAF is here
to collect an enormous amount of crashes from lots of users. Deploying own FAF might
be interesting for SysAdmins who control hundreds of machines. It also may be
quite interesting on container platform such as OpenShift.

## Watch this demo how to deploy and report into FAF quickly.
[![FAF in container](https://img.youtube.com/vi/Dz8QkrEVsvQ/0.jpg)](https://www.youtube.com/watch?v=Dz8QkrEVsvQ)

# Quickstart
We advise using [official faf-image](https://hub.docker.com/r/abrt/faf-image/).

```
docker run --name faf -dit abrt/faf-image
```

However you also probably want to mount volumes to ```/var/lib/postgres``` and
to ```/var/spool/faf``` to have a persistent database and FAF's data.

```
docker run --name faf -v /var/lib/faf-docker/faf:/var/spool/faf \
 -v /var/lib/faf-docker/postgres:/var/lib/postgres/ -dit abrt/faf-image
```

If you run FAF for the first time, then there is no database.
You have to initialize it.

```
docker exec faf init_db
```

Then FAF is ready for use.

# Reporting into deployed FAF

You can see incoming reports in web UI. It is accessible on  ```http://<container_IP>/faf```.

Finding out container IP address:
```
{% raw %}
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' faf
{% endraw %}
```

Also to send reports into your own FAF, you have to set up libreport
on all machines, from with you wish to report into your own FAF. To do so,
open a file ```/etc/libreport/plugins/ureport.conf``` and enter here:

```
URL = http://<container_IP>/faf
```

For servers, you may want to [enable automatic
reporting](https://wiki.centos.org/TipsAndTricks/ABRT#head-07813004d4363df00c50ba18e8b9457cda954dd3).
Those reports are called [μReport](http://abrt.readthedocs.io/en/latest/ureport.html#ureport)s. Run:

```
abrt-auto-reporting yes
```

# Supported operating systems

This image is ready to accept reports from all active releases of Fedora.

To add a new version of the already supported system you run:
```
docker exec -u faf faf faf releaseadd -o OPSYS --opsys-release RELEASE
```
E.g., To add Fedora 99 you run:
```
docker exec -u faf faf faf releaseadd -o fedora --opsys-release 99
```
You can even [write a plugin](https://github.com/abrt/faf/wiki/Operating-System-Plugins)
for own OS. Once you wrote this plugin and installed it, you can enable it using:
```
docker exec -u faf faf faf opsysadd OPSYS
```

# Less informative reports

A problem that happened in C/C++ code comes to FAF in the form of build_id and offset.
However, for users, it is much more suitable to have file name and line.
For better understanding, please see following two images, both describing the
same report.

Only with build_id and offset:
[![without](/assets/without_packages.png)](/assets/without_packages.png)
With filename and line number:
[![with](/assets/with_packages.png)](/assets/with_packages.png)

The process of transforming build ids and offsets to file names and lines we
call retracing. To be able to do that, we need packages, from which these
crashes come.

For [retrace.fedoraproject.org](https://retrace.fedoraproject.org/) we have all Fedora
packages stored locally. It consumes several TB of storage. This Docker image
does not download any packages and therefore does not retrace any symbols. Note
that this only affects compiled code, so for example, python unhandled exceptions
are not affected.
