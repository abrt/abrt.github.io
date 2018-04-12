---
layout: post
title:  "Sidecar container for Python exceptions"
date:   2018-04-19 13:37:00 +0100
author: Martin Kutlak <mkutlak@redhat.com>
visible: 1
categories: ABRT container exception Python
---

In recent blog posts [container-exception-logger (CEL)][cel-blog] and
[catching unhandled Python exceptions][python-exceptions] we've introduced new tools for
containers. Adding the tools to your Fedora container allows you to catch Python exceptions in the
container and get logs of them to the host.

If however your containers do not run an RPM-based Linux distribution or you don't want to rebuild
your image, you can utilize a [sidecar container][sidecar-doc] and get the above-mentioned tools
into your container through it.

### Sidecar container
The sidecar container should contain Python hooks and the CEL executable. You can use this script
that will download Python hooks and the CEL from the [Github](https://github.com/abrt/) and build
the CEL executable from sources.

```bash
#!/bin/sh

RAWGITHUB="https://raw.githubusercontent.com/abrt/abrt/master/src/hooks"

# Python2 hooks
mkdir -p abrt-hooks/py2
wget -qN "$RAWGITHUB/abrt_container.pth" -P abrt-hooks/py2
wget -qN "$RAWGITHUB/abrt_exception_handler_container.py" -P abrt-hooks/py2

# Python3 hooks
mkdir -p abrt-hooks/py3
wget -qN "$RAWGITHUB/abrt3_container.pth" -P abrt-hooks/py3
wget -qN "$RAWGITHUB/abrt_exception_handler3_container.py" -P abrt-hooks/py3

# Binary container-exception-logger
mkdir -p abrt-hooks/bin
BASEDIR=$(pwd)
TMPDIR=$(mktemp -d)
pushd $TMPDIR
git clone https://github.com/abrt/container-exception-logger.git abrt-cel ; cd abrt-cel
make
cp src/container-exception-logger $BASEDIR/abrt-hooks/bin
popd

echo "Done"
```


Example of Dockerfile for the sidecar container with the Python hooks and CEL.
```
FROM alpine:latest

RUN mkdir -p /abrt/bin /tmp/abrt-hooks

# Copy hook files
# abrt-hooks/
# ├── bin
# │  └── container-exception-logger
# ├── py2
# │  ├── abrt_container.pth
# │  └── abrt_exception_handler_container.py
# ├── py3
# │  ├── abrt3_container.pth
# │  └── abrt_exception_handler3_container.py
# └── usercustomize.py
COPY abrt-hooks /tmp/abrt-hooks

# Copy script
# files/
# └── usr
#    └── local
#       └── bin
#          └── abrt-cp.sh
COPY files /

VOLUME ["/abrt"]
RUN chmod -R 777 /abrt /tmp/abrt-hooks
CMD [ "/bin/sh", "/usr/local/bin/abrt-cp.sh" ]
```
The sidecar container includes a simple script that is run when the container starts. The script
just copies files to a shared volume and then goes into a sleep loop.

```bash
# abrt-cp.sh
#!/bin/sh
cp -R /tmp/abrt-hooks/* /abrt
while true; do sleep 300; done
```

Another important part that is in the sidecar container is [usercustomize][usercust-doc] module.
The module takes care of adding directories with Python hooks to a `sys.path`.

```python
# usercustomize.py
import os
import site
from sys import version_info

hook_path = os.path.dirname(os.path.abspath(__file__))
if version_info[0] == 2:
    site.addsitedir(os.path.join(hook_path, "py2"))
else:
    site.addsitedir(os.path.join(hook_path, "py3"))
```
### Setting up the container

First, we get current values of **PATH** and **PYTHONPATH** variables in our main container. The
PYTHONPATH variable is used to import the `usercustomize` module; the PATH is needed for the CEL
executable.

```
$ oc exec <pod> -it -- sh -c 'echo PATH: $PATH; echo PYTHONPATH: $PYTHONPATH'
PATH: /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
PYTHONPATH:
```

Once we know the values we can append to them paths of [Python hooks][python-exceptions]
and the [CEL][cel-github] executable. It is possible to skip this step and edit the variables
manually in the deployment config.

<pre>
$ oc env -c &lt;container> dc/&lt;name> PYTHONPATH=<b>/abrt</b> \
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin<b>:/abrt/bin</b>
deploymentconfig "&lt;name>" updated
</pre>

In the deployment config, we add the sidecar container and share the volume between the two
containers. We can also edit the **PATH**, **PYTHONPATH** variables here.
<pre>
$ oc edit dc/&lt;name>;
spec:
  containers:
  - name: main_container
    env:
    - name: PYTHONPATH
      value: <b>/abrt</b>
    - name: PATH
      value: /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin<b>:/abrt/bin</b>
    ...
    volumeMount:
    - name: <b>abrt-volume</b>
      path: <b>/abrt</b>
  ...
  - name: sidecar
    ...
    volumeMount:
    - name: <b>abrt-volume</b>
      path: <b>/abrt</b>
  ...
  volumes:
  - emptyDir: {}
    name: <b>abrt-volume</b>
  ...
</pre>

Once the deployment finishes, we can check whether the path to the hooks was added to site
directories.
<pre>
$ python2 -m site
sys.path = [
    '/',
    <b>'/abrt',
    '/abrt/py2',</b>
    '/usr/lib/python2.7',
    '/usr/lib/python2.7/plat-x86_64-linux-gnu',
    '/usr/lib/python2.7/lib-tk',
    '/usr/lib/python2.7/lib-old',
    '/usr/lib/python2.7/lib-dynload',
    '/usr/local/lib/python2.7/dist-packages',
    '/usr/lib/python2.7/dist-packages',
]
USER_BASE: '/.local' (doesn't exist)
USER_SITE: '/.local/lib/python2.7/site-packages' (doesn't exist)
ENABLE_USER_SITE: True
</pre>

Now that we see the path was added correctly we can test if the hooks work. To do that we will
cause an exception in our main container.
```
$ python2 -c 0/0
Traceback (most recent call last):
  File "<string>", line 1, in <module>
ZeroDivisionError: integer division or modulo by zero
```

If we check the journal log on the host we should be able to see the error message.
```
$ journalctl -xe
Apr 3 14:15:92 work dockerd-current[1175]: container-exception-logger - {
"executable": "python -c ...",
"backtrace": "<string>:1:<module>:ZeroDivisionError: integer division or modulo by zero
Traceback (most recent call last):
File \"<string>\", line 1, in <module>
ZeroDivisionError: integer division or modulo by zero
Local variables in innermost frame:
__builtins__: <module '__builtin__' (built-in)>
__name__: '__main__'
__doc__: None
__package__: None",
"pid": 36,
"reason": "<string>:1:<module>:ZeroDivisionError: integer division or modulo by zero",
"time": "6535897932",
"type": "Python"}
```

[cel-github]: https://github.com/abrt/container-exception-logger "Link to a container-exception-logger github page."
[cel-blog]: https://abrt.github.io/abrt/container/exception/docker/cel/2018/04/11/container-exception-logger/ "Link to a blogpost about container-exception-logger."
[python-exceptions]: https://abrt.github.io/openshift/abrt/python/docker/2018/02/22/abrt-for-docker/ "Link to blogpost about catching unhandled Python exceptions in containers."
[sidecar-doc]: https://kubernetes.io/docs/tasks/access-application-cluster/communicate-containers-same-pod-shared-volume/
[usercust-doc]: https://docs.python.org/3/library/site.html
