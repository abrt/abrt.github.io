---
layout: post
title:  "Catching Python3 exceptions in RHEL7"
date:   2017-07-04 10:30:00 +0100
author: Matej Habrnal
visible: 1
categories: Python3 RHEL7 abrt
---
Python3 was not available in RHEL7 for a long time hence ABRT did not contain
addon for catching such exceptions.

Since Python3 (python34 package) has been introduced in epel7 we added the
python3 addon to epel7 so now ABRT can catch Python3 exceptions, analyze
and then report them (bugzilla, FAF, email, ...).

This all is now able because we've introduced [abrt-addon-python3][pack-link]
package in epel7 repository.

All you have to do is just install ``abrt-addon-python3`` package on your RHEL7
from epel7 repository.

```
# yum install abrt-addon-python3
```

# How to test it
The easiest way how to test the new addon is by ``will_python3_raise`` python3
script which raises python3 exception. The script is part of ``will-crash`` package
(epel7 repository).

```
$ will_python3_raise
```
[pack-link]: https://koji.fedoraproject.org/koji/buildinfo?buildID=915026
