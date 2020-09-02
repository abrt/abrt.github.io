---
layout: post
title:  "FAF is dead, long live ABRT Analytics"
date:   2020-08-31 18:48:44 +0100
author: Miroslav Suchý
visible: 1
categories: FAF Analytics
---
ABRT team decided to rename [FAF](https://github.com/abrt/faf/) to new name: **ABRT Analytics**.

Disclaimer: this blogpost was written on 2019-03-28. But I forgot to publish it. Let's do that now. :)\

There were several reasons. FAF stands for Fedora Analysis Framework, but only very old versions of FAF were bound to Fedora. While we support deployment to Fedora and RHEL/CentOS only, it can analyze crashes from any system or container.

The names "FAF" and "ABRT" don't sound related, and you have to read a lot of documentation to find the relation between two. From the marketing point of view it should be evident that they are tightly connected. ABRT daemon and client tools gather crashes and reports and FAF analyzes and aggregates them.

We discussed this in the team and we had two favorites:

  * ABRT Dashboard
  * ABRT Analytics

After some time, we have chosen **ABRT Analytics** - because it shows some analysis of reports.

I [changed](https://github.com/abrt/faf/pull/759) all string FAF to ABRT Analytics in WebUI. We will start changing references in the documentation. There is **no** plan to rename GitHub projects or rename all variables and references in the code en masse as that would require big effort, and we instead focus on fixing bugs and implementing features.
