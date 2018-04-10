---
layout: post
title:  "Docker logging tool container-exception-logger (CEL)"
date:   2018-04-11 17:10:00 +0100
author: Matej Habrnal <mhabrnal@redhat.com>
visible: 1
categories: ABRT container exception Docker CEL
---
Previously, there wasn't a way how to easily get information from inside of a
container to a host. Therefore, we've developed a new tool
`container-exception-logger` which allows you to send data from the container to host log.
The main reason why we've created
this tool is to get users to know about problems which appear inside of containers.

The tool is mainly used by interpreter's default exception handlers to pass
unhandled exceptions to a host for further analysis but it can also be used for
conventional logging.

The tool is installed inside of a container and writes its input to the
container root's process stderr (`/proc/1/fd/2`), which, when docker logging
driver is set to `journald`, appears in host's journal (for more information
see [configure logging drivers]).

If you want to have your logs compatible with either OpenShift logging system
or ABRT, you should write the logs messages into
`container-exception-logger` in JSON format ([container-exception-logger format
specification]). Nevertheless, you can write the logs in any format you want.

#### Example of a message containing exception information in JSON format
```
{"type": "Python3", "executable": "/usr/bin/will_python3_raise", "reason": "will_python3_raise:3:<module>:ZeroDivisionError: division by zero", "backtrace": "will_python3_raise:3:<module>:ZeroDivisionError: division by zero\n\nTraceback (most recent call last):\n  File \"/usr/bin/will_python3_raise\", line 3, in <module>\n 0/0\nZeroDivisionError: division by zero\n\nLocal variables in innermost frame:\n__name__: '__main__'\n__doc__: None\n__package__: None\n__loader__: <_frozen_importlib_external.SourceFileLoader object at 0x7fc4568a9780>\n__spec__: None\n__annotations__: {}\n__builtins__: <module 'builtins' (built-in)>\n__file__: '/usr/bin/will_python3_raise'\n__cached__: None\n", "time": 1521454815, "pid": 23}
```

The tool tags/prefixes log messages with `"container-exception-logger - "` prefix
by default. It's possible to either change or omit the tag (see `man container-exception-logger`).


#### Example of usage
For better illustration variable `MSG` contains a log message.
'Format specification'.

```
Container:
 $ echo $MSG | container-exception-logger
Host's log:
 # journalctl -f
 Mar 19 14:59:04 localhost.localdomain dockerd-current[981]: container-exception-logger - $MSG

Container:
 $ echo $MSG | container-exception-logger --no-tag
Host's log:
 # journalctl -f
 Mar 19 15:00:27 localhost.localdomain dockerd-current[981]: $MSG

Container:
 $ echo $MSG | container-exception-logger --tag new-tag
Host's log:
 # journalctl -f
 Mar 19 15:00:27 localhost.localdomain dockerd-current[981]: new-tag - $MSG
```

[container-exception-logger format specification]: https://github.com/abrt/container-exception-logger/blob/master/README.md#format-specification
[configure logging drivers]: https://docs.docker.com/config/containers/logging/configure/
