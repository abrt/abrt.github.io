---
layout: post
title:  "ABRT in Openshift container"
date:   2017-11-24 12:18:21 +0100
author: Julius Milan <jmilan@redhat.com>
visible: 1
categories: ABRT Openshift container deploy
---
ABRT is able to run inside an Openshift resp. Docker container and catch certain subset of
problems that occured on the host where the container is running including all other
containers running on the same host.

This setup has two limitations:
* Catches just problems producing coredumps, i.e. C and C++ crashes, can't catch
Java exceptions and unhandled exceptions of interpreted languages.
* Can't catch problems outside of the physical host where ABRT container is running. In
other words it catches problems inside containers of particular host and problems
of that host itself.

# Deploy Openshift ABRT container
First we need to stop ABRT daemon on the host if it's running:
```
sudo systemctl stop abrtd
```
This is needed because ABRT container runs in privileged mode with restriction for single
access to certain system resources.

Then, assuming the Openshift cluster is running, log in as administrator and create a project
named abrt:
```
oc login -u system:admin
oc new-project abrt
```
Download [abrt-template.yaml][template-yaml-link].

Create an Openshift template from it and new app by:
```
oc create -f abrt-template.yaml
oc new-app abrt-template
```
Alternatively if you named your project differently than "abrt", create the app using template
variable as follows:
```
oc new-app abrt-template -p PROJECT=your-project-name
```

Now ABRT should be running on your host.

For verification, log into your ABRT container.
```
oc rsh abrt    # and hit the <Tab> for autocompletion.
```
Then evoke a crash inside another container or on the host itself.
For crash evocation you can use will_segfault from will-crash package which causes a
segmentation fault.
```
will_segfault
```
Now containerized ABRT should catch the problem and you should be able to see it in
ABRT container using command abrt as:
```
sh-4.3# abrt
47a41b9 1x /usr/bin/will_segfault 2017-11-28 15:29:54
```

# Deploy ABRT just in Docker
To run the same ABRT container simply in Docker, you can use following command:
```
docker run -d --privileged \
           -v /dev/log:/dev/log \
           -v /var/run/systemd/journal/socket:/var/run/systemd/journal/socket \
           -v /:/host -v /var/run/abrt:/var/run/abrt \
           --pid=host --net=host --name abrt abrt/stack-fedora
```
More details about ABRT container can be found on [dockerhub][dockerhub-abrt-link].

[dockerhub-abrt-link]: https://hub.docker.com/r/abrt/stack-fedora/
[template-yaml-link]: {{ "/files/abrt-template.yaml" | prepend: site.baseurl }}
