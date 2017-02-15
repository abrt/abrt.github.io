---
layout: post
title:  "ABRT client ansible role"
date:   2017-02-14 12:48:44 +0100
author: Matej Habrnal <mhabrnal@redhat.com>
visible: 1
categories: abrt ansible
---
Some of you maybe don't know how to install and enable ABRT. Hence, we
created ABRT ansible role which does all the required steps. Basically the
ansible role install all required packages and enable all services.

ABRT client ansible role is available in ansible galaxy and will be maintained by ABRT team.
[https://galaxy.ansible.com/abrt/abrt-client-role/](https://galaxy.ansible.com/abrt/abrt-client-role/)

How to install and run the ansible role:

1. install ABRT ansible role

```
# ansible-galaxy install abrt.abrt-client-role
```

2. create ansible playbook for example the following

```
$ cat abrt-cli.yml
```

```
---
- name: install and enable abrt client
  hosts: localhost
  roles:
  - abrt.abrt-client-role
```

3. run ansible playbook

```
$ ansible-playbook abrt-cli.yml
```

If you want, you can also test ABRT functionality for instance as follows

```
$ sleep 1000 &
```

```
$ kill -SEGV %%
```

```
# abrt-cli list
id 205449a7910ff7aa84d0cf0941a3b65c58c40e08
reason:         sleep killed by SIGSEGV
time:           Tue Feb 14 17:20:52 2017
cmdline:        sleep 1000
package:        coreutils-8.22-18.el7
uid:            1000 (mhabrnal)
count:          1
Directory:      /var/spool/abrt/ccpp-2017-02-14-17:20:52-27211
Run 'abrt-cli report /var/spool/abrt/ccpp-2017-02-14-17:20:52-27211' for creating a case in Red Hat Customer Portal

The Autoreporting feature is disabled. Please consider enabling it by issuing
'abrt-auto-reporting enabled' as a user with root privileges
```
