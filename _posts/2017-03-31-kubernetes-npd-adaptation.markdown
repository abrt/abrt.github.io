---
layout: post
title:  "ABRT integration within kubernetes"
date:   2017-03-31 14:03:18 +0100
author: Julius Milan <jmilan@redhat.com>
visible: 1
categories: abrt Kubernetes node-problem-detector
---
Monitoring of failures in [Kubernetes][kubernetes-link] nodes is now improved once more.

Part of the Kubernetes project, the [node-problem-detector][npd-link]
which purpose is to report various problems occuring in Kubernetes nodes to the
upstream layers in cluster management stack is now able to report problems detected
by ABRT. Basically any kind of problem detected by ABRT on Kubernetes host
(Uncaught Python / Java or Ruby exception, C/C++ Segmentation faults,
Kernel problems, and others) can now be reported to Kubernetes upstream layers.
Plus C/C++ Segmentation faults and Kernel problems occured on Kubernetes nodes
are catched by ABRT and reported by NPD as well.

# Technical details and workflow
Node problem detector (NPD) watches various logs, depending on its configuration,
in case of catching ABRT messages, NPD listens on systemd-journal. When some kind
of ABRT catchable problem occurs, ABRT logs into journal about detection.
After this happens NPD, which listens on journal, will recognize ABRT logs and report
them further. This functionality was achieved by creating new [configuration][conf-link]
for ABRT adaption.

# How can I use it?
Simply add abrt-adaptor.json config to your NPD config list
(--system-log-monitors parameter).

# Short demo
On [this demo][video-link], you can see how program crashes in system with running ABRT and
NPD are detected and reported upstream. Last but not least, you can see how this
functionality was tested and you can use the same approach in case you need to add,
modify or test Kubernetes/node-problem-detector configuration.

[kubernetes-link]: https://kubernetes.io/
[npd-link]: https://github.com/kubernetes/node-problem-detector
[conf-link]: https://github.com/kubernetes/node-problem-detector/blob/master/config/abrt-adaptor.json
[video-link]: https://asciinema.org/a/85icvy4mgvshplljgw9tvno3f
