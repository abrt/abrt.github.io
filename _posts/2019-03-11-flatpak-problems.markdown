---
layout: post
title:  "Reporting problems in Flatpak applications"
date:   2019-03-11 10:56:00 +0100
author: Ernestas Kulik
visible: 1
categories: ABRT container exception Flatpak Python
---

If you’ve ever experienced a crash in a Flatpak application, you might have
noticed that there is no notification coming from ABRT for it, and *maybe*
you even noticed some strange messages in the system journal:

```
abrt-server[…]: Unsupported container technology
```

The above appears when ABRT attempts to collect information about the container
(currently only Docker and LXC), if the binary has been detected to have been
run in one. For Flatpak applications, we probably get enough information already,
so we can just special-case and do nothing instead.

Unfortunately, getting things like stack traces gets a bit more complicated than
that.

For core dumps, I’ve experimented with a quick hack that reads `mountinfo` from
the dump directory<sup>[[0]](#footnotes-0)</sup>, creates a new user namespace, mounts the app and runtime
directories, and, finally, generates a trace, which is workable enough to be
used in a report, even without debug extensions installed. However, this is all
until the user decides to update, which would likely invalidate the paths in
`mountinfo`. For that, I should probably explore utilizing OSTree to check out
the known commits first.

As for Python exceptions, things don’t really work at all. But, to put things in
perspective, this is how it looks right now:

  * A path configuration file is installed; it contains only an import line for
the actual handler
  * Python opens it during initialization, executes all lines that start with
`import`, which, conveniently, causes our code to run and override `sys.excepthook`

It’s not even close to being elegant, and people are already arguing about removing
support (or, rather, coming up with a better alternative) for such things:
<https://bugs.python.org/issue33944>. To support such an implementation, we would
likely need to provide a runtime extension, or ask anyone shipping Python to include
our handler.

To make things worse, the handler communicates with ABRT using a UNIX socket, which
would at the very least require all apps to have host access. A better way would
probably be utilizing a D-Bus API, and that would require having a portal, so
that, again, we don’t have to punch holes in the sandbox.

Finally, there are still a couple unanswered questions:

What about other kinds of problems? At the moment, I can only think of Java
programs as being supported and possibly coming with their own set of challenges.

How do we handle this from the server side? Everything is built around
traditional packaging, where components belong to a specific release of a
specific operating system (distro). In the end, it could require some
rearchitecting in FAF (now ABRT Analytics) and ABRT itself.

---

<a name="footnotes-0"></a>
[0] - From that we can only infer the OSTree ref and commit. There does exist
Flatpak instance data in `XDG_RUNTIME_DIR/.flatpak`, but we cannot rely on it,
due to its garbage-collected nature.
