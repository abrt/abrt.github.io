---
layout: post
title:  "Reporting bugs to MantisBT with ABRT"
date:   2017-08-03 17:00:00 +0100
author: Martin Kutlak <mkutlak@redhat.com>
visible: 1
categories: ABRT MantisBT CentOS
---

In this blog post we will look at one of the many features of ABRT. And as you have
probably guessed from the title, the feature is MantisBT reporter.

[MantisBT](https://www.mantisbt.org/) is an open source web-based issue tracking system. It is used by CentOS
to track issues reported by users. The reporter itself is a CLI application that
allows you to report problems after ABRT detects them on your system.

### How does the reporter work
The reporter starts by searching for similar issues that already exist.
It does so by comparing values in *abrt_hash* field. *abrt_hash* contains *duplicate hash*, shortly duphash,
which ABRT uses to identify a crash across all different platforms.
If the reporter manages to find an existing issue, it adds new comment to the found issue.
Otherwise if the reporter doesn't find an existing issue, it creates a new one and
attaches backtrace and some of the other files from problem directory to it.

The *problem directory* is a directory that ABRT uses to store problem coredump
and other information about detected crash. It is usually located in `/var/spool/abrt`.

More details about how the reporter-mantisbt works can be found on our
[GitHub wiki page](https://github.com/abrt/libreport/wiki/MantisBT).


### What can you use it for
- You can report issues.
```
$ reporter-mantisbt -d PROBLEM_DIRECTORY
```

- You can upload additional files to existing issues.
```
$ reporter-mantisbt -tID FILE
```

- You can search for bug IDs using the hash from ABRT.
```
$ abrt-cli list
$ reporter-mantisbt -h HASH
```

### Configuration
If not specified otherwise, the reporter defaults to configuration from `/etc/libreport/plugins/mantisbt.conf`.
There is also possibility to create your own configuration, and then use option `-c` for reporter to use it.
```
$ reporter-mantisbt -c myown.conf -h 09205bf4a29606e12092efb771c73acc83db2ffb
```
The configuration file should follow Param=Value format. Parameters that you can specify
are Login, Password, MantisbtURL, SSLVerify, CreatePrivate, Project, and ProjectVersion.
Project and ProjectVersion are useful, if you need to use different values than from `/etc/os-release`.

The default configuration can also be edited through use of Mantisbt_PAR enviroment variables.
```
$ Mantisbt_Login=Username
$ echo $Mantisbt_Login
Username
```

### What is required
- You will need to install MantisBT libreport plugin.
```
# yum install libreport-plugin-mantisbt
```

- Account on [bugs.centos.org](https://bugs.centos.org), or on any other MantisBT instance.

Or if you have your own server and want ABRT to report the issues there, you can
find more information how to do that [here](https://github.com/abrt/libreport/wiki/Installing-MantisBT).
