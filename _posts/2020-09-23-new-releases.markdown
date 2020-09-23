---
layout: post
title:  "New releases"
date:   2020-09-23 10:00:00 +0100
author: Michal Fabík
visible: 1
categories: release features abrt abrt-java-connector gnome-abrt libreport satyr retrace-server
---

With the branching of Fedora 33, we are releasing new versions of abrt, abrt&#8209;java&#8209;connector, gnome&#8209;abrt, libreport, reportd and satyr

The current latest versions are:

+ abrt-2.14.4
+ abrt-java-connector-1.2.0
+ gnome-abrt-1.3.6
+ libreport-2.14.0
+ reportd-0.7.4
+ satyr-0.31

Most importantly, libreport's soname was bumped from `libreport.so.0` to `libreport.so.1`, which means some of the projects were re-released for compatibility reasons even though changes to their respective code bases wouldn't otherwise warrant a new release. Overall, most of the changes since the last release are only relevant to developers. Some of the more visible changes that can affect end users are listed below:  

### abrt:

+ oops-utils: Respect the 'world-readable' flag
+ plugins: abrt-dump-journal-core: Handle zstd compression
+ applet: application: Fix crash when processing deferred problems
+ dbus: Remove session objects when owner disconnects
+ python-problem: Use org.freedesktop.Problems2 API
+ abrt-console-notification: Work around noclobber
+ daemon: rpm: Use NEVRA instead of ENVRA
+ abrtd: Don't delete new problem dirs
+ Make sure that former caches are group writable

### abrt-java-connector:

+ Require java-11-devel for builds
+ Add indices to out-of-bounds exception output files
+ Adapt to different exception messages in Java 11

### gnome-abrt:

+ gnome-abrt: Load all required GIR namespaces
+ views: Unconditionally use Cairo surface for application icon
+ dbus_problems: Listen for Crash on org.freedesktop.Problems2

### libreport:

+ forbidden_words: Add potentially sensitive env vars
+ lib: Add version script for libreport
+ lib: compress: Use libarchive
+ dd: Update dd_get_owner to handle error return values
+ dirsize: Don't pick .lock'd dirs for deletion
+ gtk-helpers: Check return value
+ lib: Don’t use external executables for decompression
+ Drop Red Hat Customer Portal reporter
+ ureport: Drop Strata integration
+ lib: Remove creates-items tag parsing in event definitions

--- 

More detailed changelogs are available for [abrt](https://github.com/abrt/abrt/blob/2.14.4/CHANGELOG.md) and [libreport](https://github.com/abrt/libreport/blob/2.14.0/CHANGELOG.md).
