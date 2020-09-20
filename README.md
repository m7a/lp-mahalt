---
section: 11
x-masysma-name: mahalt
title: System Shutdown Script
date: 2020/09/20 01:30:30
lang: en-US
author: ["Linux-Fan, Ma_Sys.ma (Ma_Sys.ma@web.de)"]
keywords: ["script", "mdvl", "mahalt", "sudo"]
x-masysma-version: 1.0.0
x-masysma-repository: https://www.github.com/m7a/lp-mahalt
x-masysma-website: https://masysma.lima-city.de/11/mahalt.xhtml
x-masysma-owned: 1
x-masysma-copyright: |
  Copyright (c) 2012, 2017, 2020 Ma_Sys.ma.
  For further info send an e-mail to Ma_Sys.ma@web.de.
---
Name
====

`mahalt` -- Shutdown system after invoking scripts.

Synposis
========

	mahalt [-f] [-r]

Description
===========

This script is intended to serve as a means of shutting down desktop systems.

The following features are added compared to a direct invocation of
`systemctl poweroff` (or similar):

 * Regular users should be able to invoke shutdown. By including
   sudo configuration and a means to automatically restart the script with sudo,
   it becomes possible for users to conveniently invoke the script.
 * To perform backups and other tasks that are best run individually without
   other services interfering, directory `/usr/lib/mahalt.d` is consulted for
   executable files. They are executed in the order reported by `find | sort`.
 * Before invoking the actual shutdown command, all Java applications are sent
   a `SIGTERM` and `sync` is called.
 * If immediately before invoking the actual shutdown, `/tmp/nohalt` exists,
   the process is cancelled. This feature can be used by the scripts in
   `/usr/lib/mahalt.d` or by users on the local machine who wish to interrupt
   the shutdown process without cancelling any of the running hooks from
   `/usr/lib/mahalt.d`.

## Notes on hooks

 * All executables form `/usr/lib/mahalt.d` are passed parameter `--by-mahalt`.
 * Outputs from hooks are written to file `/var/log/mahalt` and can thus be
   analyzed after the next reboot.

## WARNING: Sudo Configuration Included

This package includes a sudoers configuration allowing user `linux-fan` to
run the `mahalt` command which makes a `shutdown`-style command available to a
user. Beware that this is not expected to be a safe operation on servers as
regular users may not have physical access.

Options
=======

----  --------------------------------------------------------
`-f`  Fast -- skip execution of hooks from `/usr/lib/mahalt.d`
`-r`  Reboot instead of shutdown
----  --------------------------------------------------------

Rationale
=========

Similar to [vifm-ext(32)](../32/vifm-ext.xhtml), this script could have been
written in a manner that would have avoided the use of sudo/full-blown root
privileges for shutdown. Modern desktop environments use `dbus`-based calls
to invoke shutdown and the regular `systemctl poweroff` nowdays often works for
unprivileged users if systemd detects that they are the only ones logged in to
the local system.

This route was not taken for the following reasons:

 * Similar to mounting, the interface for “shutdown without root” often broke
   between release upgrades.
 * Ma_Sys.ma Backups run as root as to avoid the problem of files being
   inaccessible due to missing permissions. Hence, that part would need to
   run with sudo anyways.

Bugs
====

 * File names from below `/usr/lib/mahalt.d` may not contain spaces.
 * The `killall -s TERM java` invocation may stop some processes that should
   better be managed by their respective “supervisors” like systemd or docker...

See Also
========

[halt(8)](https://manpages.debian.org/buster/sysvinit-core/halt.8.en.html)
