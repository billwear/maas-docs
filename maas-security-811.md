[note type="warning" status="Deprecated page"]
This page has been deprecated in favor of newer terminology and technology.  Please see the [updated page](/t/hardening-your-maas-installation/1381).
[/note]

Computer security is a wide-ranging and essential discipline impossible to cover completely here. There are, however, some relatively simple steps you can take to harden the security of your MAAS installation on your rack and region controllers.

[note type="caution"]
There are too many use cases and operating systems to make any meaningful security suggestions in this context for your deployed machines.
[/note]

<h2 id="heading--maas-and-root-users">`maas` and `root` users</h2>

You should pick good passwords and store them securely (e.g. in a KeePassX password database). Perform user administration only via the web UI. Only share the `maas` and `root` user passwords with administrators.

<h2 id="heading--ssl">SSL</h2>

MAAS doesn't yet support SSL natively. In the meantime, you can use a reverse SSL proxy to restrict outside access to your region controllers. Use NGINX or Apache to accept HTTPS requests, and use HTTP to communicate with MAAS via port 5240,  serving results back through HTTPS to the requester.

See [SSL](/t/ssl/764) for configuration examples.

<h2 id="heading--conf-file-permissions">Conf file permissions</h2>

MAAS configuration files should be set to have permission `640`: readable by logins belonging to the `maas` group and writeable only by the `root` user. Currently, the `regiond.conf` file contains the login credentials for the PostgreSQL database used by MAAS to keep track of all machines, networks, and configuration.

``` bash
chmod 640 /etc/maas/rackd.conf
chmod 640 /etc/maas/regiond.conf
```

After:

``` no-highlight
-rw-r----- 1 root maas   90 Sep 27 14:13 rackd.conf
-rw-r----- 1 root maas  157 Sep 27 14:14 regiond.conf
```

<h2 id="heading--firewalls">Firewalls</h2>

The [Rack controller](/t/rack-controller/771#communication-with-the-region-controller) page contains a list of ports used by MAAS for communications between rack and region controllers. Consider setting your firewall on your rack and region controllers to disallow communication on all ports except those used by MAAS.

<h2 id="heading--shared-secrets">Shared secrets</h2>

When you add a new rack or region controller, MAAS asks for a shared secret it will use to communicate with the rest of MAAS. This secret is also exposed in the web UI when you click the 'Add rack controller' button on the Controllers page.

MAAS automatically generates this secret when your first region controller installed, and stores the secret in a plain text file. 

You should also verify that any 'secret' files on region and rack controllers are `600` or readable and writeable only by the 'maas' user.

``` bash
sudo chmod 600 /var/lib/maas/secret
```

And after:

``` no-highlight
-rw------- 1 maas maas 32 Sep 27 14:15 /var/lib/maas/secret
```

<!-- LINKS -->