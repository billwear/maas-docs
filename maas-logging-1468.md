MAAS has a robust logging capability, which presents several different views, including a number of node-specific log files, and several syslogd-style text logs.  Each of these logs provides different information, or at the very least, different views of the same information.  MAAS gathers logging information the various MAAS states and records both automated and user-driven actions.

#### Quick questions you may have:

* [What are commissioning logs?](/t/commissioning-logs/1478)
* [What is the syslog logging path?](/t/maas-logging/1468#heading--path)
* [How do I use a remote syslog server?](/t/maas-logging/1468#heading--using-a-remote-syslog-server)
<!-- * [What are test logs?](/t/test-logs/1479)
* [What are machine logs?](/t/machine-logs/1480)
* [What are event logs?](/t/event-logs/1481) -->
<!-- * [What is the maas.log file?](/t/the-maas-log-file/1482)
* [What is the rackd.log file?](/t/the-rackd-log-file/1483)
* [What is the regiond.log file?](/t/the-regiond-log-file/1484)
* [What is the HTTP access log file?](/t/the-http-access-log-file/1485)
* [What is the HTTP error log file?](/t/the-http-error-log-file/1486)
* [What are the proxy log files?](/t/the-proxy-log-files/1487)
* [What are the MAAS rsyslog files?](/t/the-maas-rsyslog-files/1488) -->

The discussion of these logs and their contents can be very extensive, so each type of logging has its own documentation section, reachable from the left-hand menu -- or from the list of questions above.

<h2 id="heading--path">The syslog logging path</h2>

Syslog data is kept in `/var/log/maas/rsyslog/<machine-name><yyyy-mm-dd>/messages` (`/var/snap/maas/common/log/rsyslog/<machine-name><yyyy-mm-dd>/messages` when using the snap). Every machine known to MAAS will have corresponding syslogs.

<h2 id="heading--using-a-remote-syslog-server">Using a remote syslog server</h2>

To add a remote syslog server, click the Settings tab and then click the Network services tab. Scroll down to the Syslog section, where you can add a syslog URL or IP:

![remote_syslog](https://assets.ubuntu.com/v1/e139d4e9-installconfig-syslog__2.6-remote-syslog.png)

Click the Save button to save your changes.

[note]
Note that MAAS controllers' syslogs are not forwarded to the external syslog server -- only machine syslog information is forwarded.
[/note]

See [MAAS CLI](/t/advanced-cli-tasks/793#heading--add-or-update-a-remote-syslog-server) to learn how to add a remote syslog server using the CLI.

<!-- LINKS -->