---
layout:     post
title:      "ProcDump for Linux in Fedora"
author:     Matěj Grabovský
visible:    1
categories: fedora packages procdump
---

ProcDump is a nifty debugging utility which is able to dump the core of a running
application once a user-specified CPU or memory usage threshold is triggered. For
instance, the invocation `procdump -C 90 -p $MYPID` instructs ProcDump to monitor the
process with ID `$MYPID`, waiting for a 90 % CPU usage spike. Once it hits, it
creates the coredump and exits. This allows you to later inspect the backtrace and
memory state in the moment of the spike without having to attach a debugger to the
process, helping you determine which parts of your code might be causing performance
issues.

Originally for Windows, ProcDump has since [been ported to Linux][github] and is now
available in official package repositories for Fedora 30 and later. This means you
can install it on your machine simply using DNF with no additional hassle:

```
# dnf install procdump
```

To see ProcDump in action, head over to its [GitHub page][github] and check out the
[examples][examples] in the readme.

[ProcDump][msft] was originally created to watch for spikes in CPU usage of Windows
applications and to create coredumps when they hit. It was first released in 2009 and
is since being developed as part of the Windows Sysinternals family, gaining a wide
range of configuration options and triggers along the way.

Although ProcDump for Linux has a somewhat limited feature set for now, it should be
enough to be useful in most situations. The latest version allows you to watch for
low and high points in CPU and memory usage in a specified running process. You can
also limit how many coredumps you want to create and how much time should elapse
between them.

Although there's not yet a direct application of ProcDump in the ABRT subsystem,
we'll be on a lookout for possible use cases that might benefit the bug catching and
reporting workflow. Stay tuned for future progress on this front.

[msft]: https://docs.microsoft.com/en-us/sysinternals/downloads/procdump
[github]: https://github.com/microsoft/ProcDump-for-Linux/
[examples]: https://github.com/microsoft/ProcDump-for-Linux/#examples
