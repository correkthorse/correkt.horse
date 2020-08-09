---
layout: post
title:  "cron for systemd"
summary: "sometimes you need a cron replacement"
date: 2020-08-01 00:13:37
image: /assets/images/cover.jpg
author: tim_koopmans
categories: ops
---
Sometimes you need a cron replacement.

![systemd-run](/assets/images/2020-08-01-cron-for-systemd.png)

> Timers are systemd unit files whose name ends in .timer that control .service files or events. Timers can be used as an alternative to cron. Timers have built-in support for calendar time events, monotonic time events, and can be run asynchronously.

The following example, creates a [transient timer](https://wiki.archlinux.org/index.php/Systemd/Timers#Transient_.timer_units) unit which starts the service at 13:37 daily. This service will run the shell script passed to it.

{% highlight bash %}
$ sudo systemd-run --on-calendar="*-*-* *:13:37: /home/core/evil.sh

Running timer as unit: run-re5c49f16ddb041b0bbe45369facb3073.timer
Will run service as unit: run-re5c49f16ddb041b0bbe45369facb3073.service
{% endhighlight %}


You can view the service and timer as follows.

{% highlight bash %}
$ systemctl cat run-re5c49f16ddb041b0bbe45369facb3073.service
# /run/systemd/transient/run-re5c49f16ddb041b0bbe45369facb3073.service
# This is a transient unit file, created programmatically via the systemd API. Do not edit.
[Unit]
Description=/home/core/evil.sh
[Service]
ExecStart=
ExecStart=@/home/core/evil.sh "/home/core/evil.sh"

$ systemctl cat run-re5c49f16ddb041b0bbe45369facb3073.timer
# /run/systemd/transient/run-re5c49f16ddb041b0bbe45369facb3073.timer
# This is a transient unit file, created programmatically via the systemd API. Do not edit.
[Unit]
Description=/home/core/evil.sh
[Timer]
RemainAfterElapse=no
[Timer]
OnCalendar=*-*-* *:13:37
{% endhighlight %}

You can also list the timers at any stage as follows.

{% highlight bash %}
$ systemctl list-timers

NEXT                         LEFT       LAST                         PASSED  UNIT                                        ACTIVATES
Thu 2020-07-30 00:13:37 UTC  59min left Wed 2020-07-29 23:35:00 UTC  35s ago run-re5c49f16ddb041b0bbe45369facb3073.timer run-re5c49f16ddb041b0bbe45369facb307
{% endhighlight %}
