---
layout: post
title:  "ABRT 2.10.0 - new features"
date:   2017-01-24 15:54:00 +0100
author: Matej Habrnal
visible: 1
categories: release features
---
In the last [release](https://github.com/abrt/abrt/blob/master/CHANGELOG.md#2100) of ABRT we've introduced tons of new features. The most
interesting ones are going to be summarized in this document.

The new releases introduced a new reporter "reporter-systemd-journal" (`man
reporter-systemd-journal(1)`).

The reporter is run in notify event what means it's run after all necessary
event are run and reports the detected problems to systemd-journal in the form of
catalogue messages that contains essential problem details. The messages are
reported with `SYSLOG_IDENTIFIER=abrt-notification` and are mainly designated for
developers and administrator.

For more information about systemd-journal catalog messages see [github.com/abrt/abrt/wiki/systemd-journal-catalog-messages](https://github.com/abrt/abrt/wiki/systemd-journal-catalog-messages).

Example how the catalog report looks in systemd-journal:

[![reporter-journal screenshot](/assets/reporter_journal.png)](/assets/reporter_journal.png)

Anyway, the main reason why the reporter was introduced is better integration with [Cockpit](http://cockpit-project.org/). Cockpit shows all Error messages catched in systemd-journal in its "Logs" tab. Reports created by reporter-systemd-journal are marked with "abrt-notification" label. Those logs contain information about particular problems. 

The next image shows how a abrt-notification log looks like in Cockpit:

[![Cockpit screenshot](/assets/cockpit.png)](/assets/cockpit.png)

Another feature which was requested by user a lot - possibility to
report not reportable problems - is introduced in the new release as well.
It's done by a new global configuration option `stop_on_not_reportable`
controlled by the `ABRT_STOP_ON_NOT_REPORTABLE` environment variable.
This enables clients to force libreport to always report problems regardless of
possible sensitive data leaks and report usability concerns. See abrt's and
abrt-cli's parameter `--unsafe` (`abrt report --help` or `abrt-cli report --help`)

We also improved ABRT's security. Now, ABRT runs the core dump time backtrace
generator under the user of the crashed process not under root. This change
makes ABRT more secure to deploy as ABRT no longer runs elfutils functions in
superuser context and ABRT asks users to do security review of several more files.

Saving of core dumps to disk is now faster becase ABRT's core_pattern hook 
using low level kernel functions. ABRT also started limiting the dumped core 
file size and set the default limit to 5GiB.
The limit can be changed through the `MaxCoreFileSize` configuration option in
the `/etc/abrt/plugins/CCpp.conf` file.

The new release improves Kernel vmcore plugin as well. It extends the ability 
to parse the `kdump.conf` file at any location, so the plugin can be used from a 
container.

Tha last of the new main features is added support for JavaScript report type.
At the moment the only supported JavaScript Engine is V8 and the only supported 
ABRT type is Node.js. Support for next JavaScript engines will be added in the 
future.
