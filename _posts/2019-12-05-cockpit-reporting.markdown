---
layout:  post
title:   "ABRT in Cockpit Redux"
author:  ernestask
visible: 1
categories: Cockpit
---
Starting with version 208, Cockpit features an improved crash-reporting experience:
{% include image.html location="/assets/cockpit/Screenshot from 2019-12-11 09-50-04.png" %}

Previously, one could only report to ABRT Analytics directly, which is quite behind what is possible on the desktop, but, while adding more options like Bugzilla was not impossible per se, there were technical hurdles preventing proper use of them, namely prompts to the user being presented on the server side, leaving the reporting process hanging and allowing processes to accummulate in the background.

Enter reportd - a project, acting as a middleman between ABRT and any application that provides crash-reporting capabilities, allowing for easier implementations in system-management tools.
Currently being limited to Fedora, it will bring the same benefits to packagers and users, but hopefully reaching a wider audience.
Administrators might additionally benefit from the ability to implement their own reporting workflows for some automation or monitoring purpose.

Trying everything out is as simple as installing/upgrading Cockpit, so go on ahead and report any issues you face.
