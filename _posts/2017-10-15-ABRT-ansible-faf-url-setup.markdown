---
layout: post
title:  "FAF URL setup of ABRT client Ansible role"
date:   2017-10-15 10:31:14 +0100
author: Julius Milan <jmilan@redhat.com>
visible: 1
categories: abrt ansible faf url setup
---
We recently added a new option to ABRT client [Ansible role][ansible-link] to setup
URL of the FAF server, i.e., where crash reports from ABRT client are reported.

This small improvement will mostly appreciate people using or willing to use their
installation of FAF for gathering crash reports from ABRT client for custom analysis,
having their FAF installation running on custom server (easy to do using
[FAF ansible role][faf-ansible-link]) or [in docker container][faf-in-container-link].

# Usage
To use ABRT client Ansible role it is needed to declare it in your playbook, as:
```
   ...
   roles:
     - ansible-abrt-client-role
   ...
```
By default, FAF URL is set to ```https://retrace.fedoraproject.org/faf```, which is main FAF
installation of Fedora.
To adjust it, just put to your playbook:
```
   ...
   roles:
     - { role: ansible-abrt-client-role, faf_url: 'your.faf.url' }
   ...
```
Or, using the newer syntax:
```
   ...
   tasks:
   - include_role:
       name: ansible-abrt-client-role
     vars:
       faf_url: 'your.faf.url'
   ...
```

[ansible-link]: https://github.com/abrt/ansible-abrt-client-role
[faf-in-container-link]: https://abrt.github.io/faf/container/2017/09/20/FAF-in-container/
[faf-ansible-link]: https://github.com/abrt/ansible-role-faf
