---
layout: post
title:  "New releases"
date:   2020-02-19 12:00:00 +0100
author: Michal Fabík
visible: 1
categories: release features abrt abrt-java-connector gnome-abrt libreport satyr retrace-server
---

Just prior to branching of Fedora 32, we released new versions of abrt, gnome&#8209;abrt, abrt&#8209;java&#8209;connector, libreport, satyr and retrace&#8209;server.

The current latest versions are:

+ abrt-2.14.0
+ gnome-abrt-1.3.1
+ abrt-java-connector-1.1.4
+ libreport-2.12.0
+ satyr-0.30
+ retrace-server-1.21.0

The new releases come with the following changes, among others:

### abrt

+ CLI fixes
+ Avoid warnings about abrt-ccpp.service not existing during installation
+ dbus: Warn the user when GetProblems() is called with a large (>100) number of problems
+ Bring back journal catalog file for C/C++ crashes
+ Add short stack trace to the C/C++ crash journal catalog file
+ Fix abrt-dump-oops finishing with the wrong exit code

### gnome-abrt

+ Change build system to Meson
+ Do not require X11 to be available
+ GUI tweaks
[![gnome-abrt-GUI][gnome-abrt-GUI]][gnome-abrt-GUI]

### abrt-java-connector

+ Fix build failure with GCC 10

### libreport

+ Switch to Nettle for SHA digest computation
+ Fix runtime warning in reporter-rhtsupport

### satyr

+ Switch to Nettle for SHA digest computation

### retrace-server

+ Add the possibility to run retraces in a podman container
+ Rework the pre- and post-retrace hook mechanism

--- 

More detailed changelogs are available for [abrt](https://github.com/abrt/abrt/blob/2.14.0/CHANGELOG.md) and [libreport](https://github.com/abrt/libreport/blob/2.12.0/CHANGELOG.md).


[gnome-abrt-GUI]: /assets/2020-02-new-releases/gnome-abrt-GUI.png "Updated GUI example"
