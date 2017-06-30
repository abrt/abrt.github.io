---
layout: post
title:  "ABRT in Cockpit"
date:   2017-06-29 14:14:00 +0100
author: marusak
visible: 1
categories: ABRT Cockpit
---
In the upcoming release of Cockpit (144) users can now simply work with ABRT.
This integration makes it easier for admins to watch over their machines and
easily find what problems has occurred on them.

# What changes does it bring to Cockpit?
This support was integrated right into logs. Two main changes in the logs can
be spotted:
1. Filters in logs has changes to filter also problems
[![cockpit-filters](/assets/cockpit_filters.png)](/assets/cockpit_filters.png)
2. Some problems have different icon and contain data from ABRT
[![cockpit-problems](/assets/cockpit_problems.png)](/assets/cockpit_problems.png)

After opening such problem there are 3 tabs:
1. General tab - same as in each and every log
[![cockpit-general](/assets/cockpit_general.png)](/assets/cockpit_general.png)
2. Problem info - containing basic information about problem
[![cockpit-general](/assets/cockpit_info.png)](/assets/cockpit_info.png)
3. Problem details - containing details of the crash
[![cockpit-general](/assets/cockpit_details.png)](/assets/cockpit_details.png)

Also you can Report problem into [FAF](https://retrace.fedoraproject.org/faf) or delete the problem.

# Can I use it right now?
Sure you can! You will need upstream Cockpit and Fedora 26.
### Upstream Cockpit
You can simply use upstream Cockpit by following
[this few steps](https://github.com/cockpit-project/cockpit/blob/master/HACKING.md#working-on-your-local-machine)
. Also you can wait a few days for the new release.
### Fedora 26
There are some big changes in ABRT as we mentioned in this
[blogpost](https://abrt.github.io/abrt/systemd/core_pattern/2017/03/07/fedora-26-change/).
However if you install ABRT >= 2.9.0 and newest Libreport (2.9.1-2) and enable correct
services as mention in the blog, then you can use it on whichever version of Fedora,
CentOS or RHEL.

# Isn't there some demo?
Funny you should ask. You can watch it [here](https://youtu.be/GSAeLExvFhU).

## Any other plans with this?
Yes, this is the first version. We plan on adding reporting to bugtracking tools
such as Bugzilla, MantisBT and others that are supported by ABRT.
