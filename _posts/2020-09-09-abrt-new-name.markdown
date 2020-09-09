---
layout: post
title:  "New name for ABRT?"
date:   2020-09-09 10:00:00 +0100
author: Miroslav Suchý
visible: 1
categories:
---
The project ABRT started in 2019. The initial name was `CrashWatcher`. Very quickly changed to `CrashCatcher`. But in one month, it got its final name ABRT. ABRT is the [name of a POSIX signal](https://en.wikipedia.org/wiki/Signal_(IPC)#POSIX_signals) and stems from the word `abort`.

ABRT project was meant as a tool to ease the life of Red Hat Support. Unfortunately Red Hat Support never fully utilized and used ABRT (with some minor exceptions). I recently analyzed the use of ABRT, and its strength are for developers and DevOps. We can identify and helps to report bugs when new software or major release is released. Devops can leverage that we can identify crashes in their deployments and show it in a private instance of ABRT Analytics.

So we have a clear shift in the use.

The name ABRT has several cons: It is hard to pronounce. And a lot of people still remember when ABRT was unable to deduplicate bugs and spammed Bugzilla in its early days.

Therefore the ABRT team would like to rename ABRT to ... something else. To indicate new directions: orientation on developers and DevOps. But we struggle what the name should be.

Can you help us? Please [file in this form](https://docs.google.com/forms/d/e/1FAIpQLSdQfpyndofp1oIIQJ58wXuNjDXyyGbFgOuAJIubA_F8aR_83g/viewform).

The name should not be used in in some different SW project (this outs `CrashCatcher). It should be easy to pronounce. The name may be related to the function - collecting crashes.
