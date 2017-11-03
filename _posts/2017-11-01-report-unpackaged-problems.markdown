---
layout: post
title:  "Report problems from unpackaged executables to FAF server"
date:   2017-11-01 12:00:00 +0100
author: Matej Habrnal
visible: 1
categories: FAF container OpenShift uReport unpackaged
---

Until this moment it was not able to report problems from unpackaged executables
because no package information can be obtained from those executables, even a
component name which is very important for problem reporting.
In this article is described how to configure ABRT and FAF server to achieve
this.

# How to allow reporting of those problems on a client side?
This feature is introduced in ```abrt-2.10.5 (libreport-2.9.3)```.
You have to do one step to allow this feature, set the ```ProcessUnpackaged```
option to ```yes``` in ```/etc/libreport/plugins/ureport.conf```.

It can be done by following command:
```
# augtool set /files/etc/libreport/plugins/ureport.conf/ProcessUnpackaged yes
```

## Catching problems from unpackaged executables
If you want to report problems which come from unpackaged executables,
you have to, at first, allow catching such problems. It can be done be
editing of ```/etc/abrt/abrt-action-save-package-data.conf```, option
```ProcessUnpackaged``` has to have ```yes``` value.

It can be done by following command:
```
# augtool set /files/etc/abrt/abrt-action-save-package-data.conf/ProcessUnpackaged yes
```

You can test catching of unpackaged problems by following commands:
```
$ echo -e "#include <stdlib.h> \nint main(){abort();}" | \
    gcc -xc -o /tmp/crash_unpack - ; \
    /tmp/crash_unpack; rm -f /tmp/crash_unpack
Segmentation fault (core dumped)

$ abrt-cli ls
...
id b03171d429ee5a9220435a7606ee71ff83752458
reason:         crash_unpack killed by SIGABRT
time:           Thu 02 Nov 2017 04:38:47 PM CET
cmdline:        /tmp/crash_unpack
uid:            1000 (mhabrnal)
count:          3
Directory:      /var/spool/abrt/ccpp-2017-11-02-16:38:47.202850-3903
...
```

## Python scripts executed from relative path
If you want to catch unhandled python exception from scripts which are run
without full path in sys.argv[0], you have to set option
```RequireAbsolutePath``` to ```no``` in ```/etc/abrt/plugins/python3.conf```
(respectively ```/etc/abrt/plugins/python.conf``` for Python2).

It can be done by following command:
```
# augtool set /files/etc/abrt/plugins/python3.conf/RequireAbsolutePath yes
```

## How to report problems automatically?
If you want to report all problem automatically, use ```abrt-auto-reporting``` tool
to allow this.

```
# abrt-auto-reporting enabled
```

# How to allow receiving of those problems on FAF server?
If you are using FAF in a container [FAF in
a container](https://abrt.github.io/faf/container/2017/09/20/FAF-in-container/),
receiving problems from unpackaged executables is enabled by default.

In other cases, you have to enable it in ```/etc/faf/faf.conf``` (```allow-unpackaged
= True```).
