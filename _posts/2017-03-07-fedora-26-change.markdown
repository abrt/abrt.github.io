---
layout: post
title:  "ABRT in Fedora 26 with systemd-coredump"
date:   2017-03-07 12:48:44 +0100
author: Matej Habrnal <mhabrnal@redhat.com>
visible: 1
categories: abrt systemd core_pattern
---
Enabling [systemd-coredump][coredumpctl] by default is from the perspective of ABRT the
biggest change in Fedora 26.

# How ABRT works?
There is a service `abrt-ccpp.service` which runs
`/usr/sbin/abrt-install-ccpp-hook install` that causes ABRT's
`kernel.core_pattern` hook to be installed. What it basically does
is that it saves current `kernel.core_pattern` to `/var/run/abrt/saved_core_pattern`
and replaces it with string `|/usr/libexec/abrt-hook-ccpp %s %c %p %u %g %t %P %I`.
This causes that all coredumps are processed by ABRT's hook
which by default dumps core into ABRT's problem directories which are created
in `/var/spool/abrt`. After processing is finished you can use `abrt` command line
tool for further analysis (remote backtrace generating, run GDB against the
problem, print information about the problem and so on). For more information
about the tool see `man abrt`.

# How will ABRT work in Fedora 26?
`abrt-ccpp.service` won't be enabled so default `kernel.core_pattern` won't be
replaced by ABRT's one.
It means, coredumps will be processed by `systemd-coredump` and metadata about
coredumps will be stored in the systemd journal. The `abrt-ccpp.service` service
will be replaced by `abrt-journal-core.service`. This service extracts coredumps
and other important information about problems from systemd journal.

# How does it affect ABRT users?
ABRT users shouldn't experience any changes. Problem directories will be still
created in `/var/spool/abrt` and all will work like in older Fedoras.
If users want to use ABRT's hook instead of the systemd one they can easily
disable `abrt-journal-core.service` and enable `abrt-ccpp.service` by running
following commands:

```
# systemctl disable abrt-journal-core.service
# systemctl stop abrt-journal-core.service
# systemctl enable abrt-ccpp.service
# systemctl start abrt-ccpp.service
```

[coredumpctl]: https://fedoraproject.org/wiki/Changes/coredumpctl
