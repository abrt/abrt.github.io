---
layout: post
title:  "FAF in OpenShift"
date:   2017-10-17 12:00:00 +0100
author: mkutlak
visible: 1
categories: FAF container OpenShift
---

Guide on how to deploy your own [FAF](https://www.github.com/abrt/faf) in [OpenShift](https://www.openshift.com/).

# Deploy FAF in OpenShift

The following text will guide you through the process of deploying FAF in OpenShift and using it to collect crash reports from your machines.
There are two options how you can do it. The first option is to use the command line, and the second one is to use the web interface.

## Prerequisites
 - [OpenShift Origin](https://github.com/openshift/origin/releases) or [OpenShift Container Platform](https://www.openshift.com/).
 - Cluster admin rights for editing security context constraints, and managing
   volumes.


## Command Line

1. **Create a new project.**
```
oc new-project <project_name>
```

2. **Add the FAF application.** <br>
We've created docker image with FAF, that you can find on
[Docker Hub](https://hub.docker.com/r/abrt/faf-image).
```
oc new-app abrt/faf-image:1.2.1 --name <app_name>
```
This command will pull the image from docker hub and then deploy it.

3. **Edit Security Context Constraints.** <br>
The deployment won't succeed because the FAF container requires usage of the root user, but default
configuration doesn't allow it. To allow the container to use of root, create a new service account
and add it to **anyuid's** security context constraints.
```
oc create serviceaccount <sa_username>
oc adm add-scc-to-user anyuid -z <sa_username>
oc patch dc/<app_name> --patch '{"spec":{"template":{"spec":{"serviceAccountName": "<sa_username>"}}}}'
```
Changing deployment configuration will trigger new container deployment. This time, it should succeed,
and the FAF should be running. To make it accessible to external clients, you need to expose the service.

4. **Expose the service.**
```
oc expose service <app_name> --path=/faf
```
Now the service should be accessible to external clients on address you can get with:
```
oc get route <app_name>
```


## Web Interface

It is also possible to deploy FAF through the web interface, except for editing security context
constraints. You will need to use the command line for it.

1. **Create a new project.**
[![create_project][create_project]][create_project]

2. **Add FAF's docker image to project.** Use image name ```abrt/faf-image:1.2.1```.
[![add_docker_image][add_docker_image]][add_docker_image]

3. **Create a new service account and add it to anyuid's SCC.**
```
oc create serviceaccount <sa_username>
oc adm policy add-scc-to-user anyuid -z <sa_username>
oc patch dc/<app_name> --patch '{"spec":{"template":{"spec":{"serviceAccountName": "<sa_username>"}}}}'
```
The FAF should now be running.
[![success_deploy][success_deploy]][success_deploy]

4. **Expose the service.** <br>
```Applications```->```Routes``` and ```Create Route```.
[![create_route][create_route]][create_route]


# Persistent storage for FAF

Containers are not persistent by default; their contents get cleared, when you restart them.
Default claimed volume for the pod is EmptyDir, which is a ephemeral directory that will get cleared, once the pod is terminated.
Again, you have an option to do this either using the command line or the web interface.

### Command line

**Update the volume configuration:** <br>
These commands will change the default EmptyDir volumes to persistent ones.
```
oc volume dc/<app_name> --add --overwrite --name=faf-1 -t=pvc -m=/var/lib/postgres --claim-name=faf-database --claim-size=5Gi --claim-mode="ReadWriteOnce"
oc volume dc/<app_name> --add --overwrite --name=faf-2 -t=pvc -m=/var/spool/faf --claim-name=faf-report-data --claim-size=5Gi --claim-mode="ReadWriteOnce"
```

### Web interface
1. **Create claims for storage.** One for database and another for FAF's data.
<br>
```Storage``` -> ```Create Storage```
[![create_storage][create_storage]][create_storage]

2. **Remove mounted volumes.**
<br>
```Applications``` -> ```Deployments```. Then choose the FAF deployment and switch to ```Configuration``` tab.
[![container_volumes][container_volumes]][container_volumes]

3. **Add a new storage for the database and FAF's data.**
[![db_storage][db_storage]][db_storage]
The mount path for database is ```/var/lib/postgres```, and ```/var/spool/faf``` for FAF's data.


# Reporting into deployed FAF

To be able to report to your newly created FAF instance, you will have to change [µReport](https://github.com/abrt/faf/wiki/uReport)
configuration in ```/etc/libreport/plugins/ureport.conf``` on every machine from which you want to gather the crash reports.
You can utilize Ansible role ABRT client as stated [here](https://abrt.github.io/abrt/ansible/faf/url/setup/2017/10/15/ABRT-ansible-faf-url-setup/).

```
...
URL = http://<address>/faf
...
```
As was mentioned in the [previous blog post](https://abrt.github.io/faf/container/2017/09/20/FAF-in-container/#less-informative-reports), the crash reports
from compiled packages will be less informative than those you can find on [retrace.fedoraproject.org](https://retrace.fedoraproject.org).


[add_docker_image]: /assets/faf-in-oc/add_docker_image.png "Add FAF docker image from Docker Hub."
[create_project]: /assets/faf-in-oc/create_project.png "Create new project."
[success_deploy]: /assets/faf-in-oc/success_deploy.png "Successfuly deployed FAF docker image."
[create_storage]: /assets/faf-in-oc/create_storage.png "Create a new claim for storage space."
[container_volumes]: /assets/faf-in-oc/container_volumes.png "Mounted container volumes. Applications/Deployments/Configuration"
[db_storage]: /assets/faf-in-oc/db_storage.png "Add a new storage for container. Database /var/lib/postgres, FAF's reports /var/spool/faf"
[create_route]: /assets/faf-in-oc/create_route.png "Expose the service to external clients by creating route to it."
