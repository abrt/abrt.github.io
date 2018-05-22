---
layout: post
title:  "Java exceptions from container"
date:   2018-05-22 15:15:00 +0100
author: Matej Habrnal <mhabrnal@redhat.com>
visible: 1
categories: ABRT container exception Java
---
As you know from a previous blog [post], lately we have been dealing with
catching unhandled exceptions from inside of containers.

In this blog post, we would like to introduce a new language we are
capable of doing that for. The language is `JAVA`.

We've taken ABRT's tool [abrt-java-connector] which catches java's exceptions
and we've added a new option `cel` there. The `cel` option turns on writing
exceptions to [container-exception-logger]. For more information about
`abrt-java-connector` options see [abrt-java-connector readme].
Also, a new package `abrt-java-connector-container` was released. It contains
a minimal set of files needed for container exception logging.

#### How to make it work in a container
If you use executables which load `/usr/share/java-utils/java-functions` file
(for instance `will_java_throw` shipped by package [will-crash]), you only need
to have package `abrt-java-connector-container` installed. This is default in
Fedora.

Content of `will_java_throw` executable:

```bash
$ cat /usr/bin/will_java_throw
 #!/bin/sh
 MAIN_CLASS="WontCatchNullPointerException"
 export MAIN_CLASS
 . /usr/share/java-utils/java-functions
 set_classpath "willcrash/willuncaught.jar"
 run
```

In other cases you have to load shared native library (.so)
`/usr/lib/abrt-java-connector/libabrt-java-connector.so` into VM using either
`-agentlib` or `-agentpath` command line parameter. The first
parameter requires the library's file name and works only if the library is
placed in one of the ld searched directories or a directory included in
`LD_LIBRARY_PATH` environment variable. The latter command line argument works
with an arbitrary, valid absolute path pointing to the library.

```bash
$ java -agentlib:abrt-java-connector=cel=on $MyClass -platform.jvmtiSupported true
```

For more information see [abrt-java-connector readme].

#### Example (exception in container -> host's systemd journal log)
##### Inside of container:
```
[root@049e3e4b7233 tmp]# will_java_throw
Can't open '/usr/share/abrt/conf.d/plugins/java.conf': No such file or directory
Can't open '/etc/abrt/plugins/java.conf': No such file or directory
Exception in thread "main" java.lang.NullPointerException
	at WontCatchNullPointerException.die_hard(WontCatchNullPointerException.java:30)
	at WontCatchNullPointerException.die_hard(WontCatchNullPointerException.java:33)
	at WontCatchNullPointerException.die_hard(WontCatchNullPointerException.java:33)
	at WontCatchNullPointerException.die_hard(WontCatchNullPointerException.java:33)
	at WontCatchNullPointerException.die(WontCatchNullPointerException.java:21)
	at WontCatchNullPointerException.main(WontCatchNullPointerException.java:38)
```

##### Host's systemd journal:
```bash
# journalctl -f
May 22 14:40:19 localhost.localdomain dockerd-current[972]:
container-exception-logger - {"type": "Java", "executable":
"/usr/share/java/willcrash/willuncaught.jar", "reason": "Uncaught exception
java.lang.NullPointerException in method
WontCatchNullPointerException.die_hard()", "backtrace": "Exception in thread
"main" java.lang.NullPointerException\n        at
WontCatchNullPointerException.die_hard(WontCatchNullPointerException.java:30)
[jar:file:/usr/share/java/willcrash/willuncaught.jar!/WontCatchNullPointerException.class]\n
at
WontCatchNullPointerException.die_hard(WontCatchNullPointerException.java:33)
[jar:file:/usr/share/java/willcrash/willuncaught.jar!/WontCatchNullPointerException.class]\n
at
WontCatchNullPointerException.die_hard(WontCatchNullPointerException.java:33)
[jar:file:/usr/share/java/willcrash/willuncaught.jar!/WontCatchNullPointerException.class]\n
at
WontCatchNullPointerException.die_hard(WontCatchNullPointerException.java:33)
[jar:file:/usr/share/java/willcrash/willuncaught.jar!/WontCatchNullPointerException.class]\n
at WontCatchNullPointerException.die(WontCatchNullPointerException.java:21)
[jar:file:/usr/share/java/willcrash/willuncaught.jar!/WontCatchNullPointerException.class]\n
at WontCatchNullPointerException.main(WontCatchNullPointerException.java:38)
[jar:file:/usr/share/java/willcrash/willuncaught.jar!/WontCatchNullPointerException.class]\n",
"uid": "0", "abrt-java-connector": "1.1.1-3ec4e6c"}
```


[container-exception-logger]: https://abrt.github.io/abrt/container/exception/docker/cel/2018/04/11/container-exception-logger/
[abrt-java-connector]: https://github.com/abrt/abrt-java-connector
[abrt-java-connector readme]: https://github.com/abrt/abrt-java-connector/blob/master/README
[will-crash]: https://github.com/sorki/will-crash
[post]: https://abrt.github.io/openshift/abrt/python/docker/2018/02/22/abrt-for-docker/

