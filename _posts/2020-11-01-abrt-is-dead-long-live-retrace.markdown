---
layout: post
title:  "ABRT is dead - long live Retrace"
date:   2020-11-01 12:00:00 +0100
author: Miroslav Suchý
visible: 1
categories:
---

A few weeks ago, I noted that [we are looking for a new name for ABRT](/2020/09/09/abrt-new-name/). After a discussion, we come to the name **Retrace**.

It has several benefits:

 * It is not an entirely new name. Part of the ABRT is [retrace-server](https://github.com/abrt/retrace-server/). We have a public server called [retrace](https://retrace.fedoraproject.org/).
 * It is easy to pronounce.
 * It reflects what we are doing: finding and reporting tracebacks.

What will be the names of components:

 * "abrt" is going to be renamed to "retrace".
 * "abrt analytics" (formerly faf) will be renamed to "retrace analytics".
 * "retrace server" will keep its name.
 * "abrt-java-connector" is going to be renamed to "retrace-java-connector"
 * "ansible-role-faf" is going to be renamed to "ansible-role-retrace-analytics"
 * "ansible-abrt-client-role" is going to to be renamed to "ansible-retrace-client-role"
 * "libreport" and "satyr" will keep their names.

Git Hub project will be migrated to FIXME ("retrace software" or "Red Hat Retrace" are available. Simple retrace is taken). Our blog will be moved to FIXME (depends on the name of organization).
Repositories will be renamed. Documentation is going to be changed.
Variables and code will not be altered with only one exepction - the name of user facing executables will be altered with some transition period when both names will work.  Newly created code will use the new name.


