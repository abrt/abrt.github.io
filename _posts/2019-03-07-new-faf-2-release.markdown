---
layout: post
title:  "New FAF 2.0.0 released"
date:   2019-03-07 10:10:10 +0100
author: Martin Kutlak
visible: 1
categories: release features faf
---

The latest [release](https://github.com/abrt/faf/blob/2.0.0/CHANGELOG.md) of FAF comes with two major changes.

+ The first one is that we've decided to drop the support of Python2 code.
+ The second one is an abandonment of YUM in favour of DNF.


Since release [1.3.4](https://github.com/abrt/faf/blob/1.3.4/CHANGELOG.md) we've also migrated from [fedmsg](http://fedmsg.com/)
to [fedora-messaging](https://fedora-messaging.readthedocs.io/en/stable/) infrastructure.

## Where to get the new release:
If you want to install the new FAF you can get it from our **COPR**: [@abrt/faf-el8](https://copr.fedorainfracloud.org/coprs/g/abrt/faf-el8/)

or use our Docker image: [docker/abrt/faf-image](https://cloud.docker.com/u/abrt/repository/docker/abrt/faf-image).

---
If you still need the old Python2 code, it is available in our Github - [github@abrt/faf/el7](https://github.com/abrt/faf/tree/el7).
