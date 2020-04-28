---
layout: post
title: "Integrating ABRT directly into your project with sABRTooth"
author: ernestask
visible: 1
categories: sABRTooth
---
ABRT is designed to only handle crashes on an operating-system level, i.e.
it only handles packaged binaries by default and captures crashes coming from
all of them. You might set up a blacklist, but that is rather tedious to get
right if your only goal is to monitor the health of your own application.

sABRTooth, currently in experimental phase, was born out of the growing need to
track issues in cloud applications. With only a running instance of
ABRT Analytics and minimal changes in your code required, you can build basic
monitoring infrastructure for your needs.

Since ABRT Analytics is not designed with this in mind, we would need
improvements in the longer-term to enable easier single-use deployments. Another
goal is providing support for a wider range of languages (currently only Python).

To try it out, [grab the code](https://github.com/abrt/sABRTooth-python/) and
take a look at the [documentation](https://sabrtooth-python.readthedocs.io/).

Here is a quick demo of it in action:
{% include youtube.html id="tf0MTM3qIfg" %}
