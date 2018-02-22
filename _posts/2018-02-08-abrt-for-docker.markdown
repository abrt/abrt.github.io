---
layout: post
title:  "Catching unhandled Python exception in Docker (OpenShift)"
date:   2018-02-22 10:10:00 +0100
author: Matej Habrnal <mhabrnal@redhat.com>
visible: 1
categories: OpenShift ABRT Python Docker
---
We are dealing with a question how to detect and catch unhandled python exception
inside of containers and collect/display them on the host (OpenShift, logs, ABRT, ...).

I would like to say this is a very first version of a new ABRT's tool,
so feel free to come with new ideas or comments.

OpenShift uses fluentd and Elasticsearch for aggregating container logs
[OpenShift logging]. The perfect solution should be easily integrated with those tools.

We've created an rpm ([python3-abrt-container-addon]) which ships all needed. All
you have to do is just install the rpm into a container or add it into an image.

For instance, add the following line into your Dockerfile:
```
RUN rpm -ivh https://fedorapeople.org/~mhabrnal/abrt-for-docker/python3-abrt-container-addon-2.10.6-1.fc27.x86_64.rpm
```

or download the rpm ([python3-abrt-container-addon]) to your docker build
directory, and add the following lines into your Dockerfile:
```
ADD python3-abrt-container-addon-2.10.6-1.fc27.x86_64.rpm /tmp/python3-abrt-container-addon-2.10.6-1.fc27.x86_64.rpm
RUN rpm -ivh /tmp/python3-abrt-container-addon-2.10.6-1.fc27.x86_64.rpm
```

If you have Fedora >= 27 as a base image, you can install the ABRT tool by DNF
(for Fedora 27 `python3-abrt-container-addon` the rpm is pending in [Bodhi])
```
RUN dnf install python3-abrt-container-addon
```

Once, you are running Docker with the rpm installed, everything start work
immediately, you don't have to do anything else. All unhandled python3 exceptions
are logged out of the container.

In the following image, you can see logs of some exceptions in OpenShift pod logs
```
1. exception rised by command '$ python3 -c 0/0'
2. exception rised by 'will_python3_raise' script
```
[![img_os_log][img_os_log]][img_os_log]

If you have logging system deployed in your OpenShift [OpenShift logging], fluentd aggreates all ABRT's logs and sends them to Elasticsearch. You can browse them in kibana:
[![img_kibana][img_kibana]][img_kibana]

[python3-abrt-container-addon]: https://fedorapeople.org/~mhabrnal/abrt-for-docker/python3-abrt-container-addon-2.10.3-1.fc27.x86_64.rpm "python3-abrt-container-addon"
[Bodhi]: https://bodhi.fedoraproject.org/updates/abrt-2.10.6-2.fc27
[OpenShift logging]: https://docs.openshift.org/latest/install_config/aggregate_logging.html "OpenShift logging"
[img_os_log]: /assets/abrt-for-docker/os-log2.png "ABRT's logs in OpenShift"
[img_kibana]: /assets/abrt-for-docker/kibana2.png "Kibana ABRT's logs"


