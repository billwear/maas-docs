This page describes how to provide high availability (HA) for MAAS at both region and rack levels.  In the context of MAAS, there are four types of HA: BMC (node power cycling); DHCP; PostgreSQL; and API services.

#### Quick questions you might have:

* [How do I make rack controllers highly available?](/t/high-availability/804#heading--rack-controller-ha)
* [How do I enable highly-available BMC?](/t/high-availability/804#heading--bmc-ha)
* [How do I enable highly-available DHCP services?](/t/high-availability/804#heading--dhcp-ha)
* [How do I make region controllers highly available?](/t/high-availability/804#heading--region-controller-ha)
* [How do I enable highly-available PostgreSQL?](/t/high-availability/804#heading--postgresql-ha)
* [How do I enable highly-available API services?](/t/high-availability/804#heading--secondary-api-servers)
* [How do I enable load balancing for API services?](/t/high-availability/804#heading--load-balancing-with-haproxy-optional)
* [How do I install extra rack controllers?](/t/rack-controller/771#heading--install-a-rack-controller)
* [How do I enable HA in a snap?](/t/high-availability/804#heading--snap)

<h2 id="heading--rack-controller-ha">Make rack controllers highly available</h2>

You need to [install multiple rack controllers](/t/rack-controller/771#heading--install-a-rack-controller) to achieve real high availability.  Once that's done, you automatically gain highly-available BMC control, and you can also enable highly-available DHCP.

<h3 id="heading--bmc-ha">Enable highly-available BMC</h3>

HA for BMC control (node power cycling) is provided out-of-the-box, once a second rack controller is present. MAAS will automatically identify which rack controller is responsible for a BMC and set up communication accordingly.

<h3 id="heading--dhcp-ha">Enable highly-available DHCP services?</h3>

DHCP HA affects the way MAAS manages node, including enlistment, commissioning and deployment. It enables primary and secondary DHCP instances to serve the same VLAN. This VLAN replicates all lease information is between rack controllers. MAAS-managed DHCP is a requirement for DHCP HA.

If you are enabling DHCP for the first time after adding a second rack controller, please read [Enabling DHCP](/t/dhcp/759#heading--enabling-dhcp).  On the other hand, if you have already enabled DHCP on your initial rack controller, you'll need to reconfigure DHCP.

Access the appropriate VLAN (via the 'Subnets' page) and choose action 'Reconfigure DHCP'. There, you will see the second rack controller in the 'Secondary controller' field. All you should have to do is press the 'Reconfigure DHCP' button:

![reconfigure DHCP](https://assets.ubuntu.com/v1/c3d1e0c1-manage-ha__2.6-ha-dhcp.png)

<h3 id="heading--multiple-region-endpoints">Configure multiple region endpoints</h3>

MAAS will automatically discover and track all reachable region controllers in a single cluster of rack controllers  It will also attempt to automatically connect to them if the one in use becomes inaccessible.  Administrators can alternatively specify multiple region-controller endpoints for a single rack controller by adding entries to `/etc/maas/rackd.conf`.  For example:

    .
    .
    .
    maas_url:
      - http://<ip 1>:<port>/MAAS/
      - http://<ip 2>:<port>/MAAS/
    .
    .
    .

The setup of highly-available DHCP is now complete.  Note that, for HA purposes, DHCP provisioning will take into account multiple DNS services when there is more than one region controller on a single region.

<h2 id="heading--region-controller-ha">Make region controllers highly available</h2>

Implementing highly-available region control involves setting up two highly-available services:

-   PostgreSQL HA
-   Secondary API server(s)

Load balancing is optional, but is highly recommended.

<h3 id="heading--postgresql-ha">Enable highly-available PostgreSQL</h3>

MAAS stores all state information in the PostgreSQL database. It is therefore recommended to run it in HA mode. Configuring HA for PostgreSQL is external to MAAS. You will, therefore, need to study the [PostgreSQL documentation](https://www.postgresql.org/docs/9.5/static/high-availability.html) and implement the variant of HA that makes you feel most comfortable.

A quick treatment of [PostgreSQL HA: hot standby](/t/postgresql-ha-hot-standby/803) is provided here for convenience only. This summary will give you an idea of the command line implementation of HA with PostgreSQL.

Each region controller uses up to 40 connections to PostgreSQL in high load situations. Running two region controllers requires no modifications to the `max_connections` in `postgresql.conf`. More than two region controllers require that `max_connections` be adjusted to add 40 more connections per added region controller.

<h3 id="heading--secondary-api-servers">Enable highly-available API services</h3>

Please see [Region controllers](/t/region-controller/772) and [Multiple region endpoints](#heading--multiple-region-endpoints) for more information about how to install and configure rack controllers for multiple region controllers.

<h3 id="heading--load-balancing-with-haproxy-optional">Load balancing with HAProxy (optional)</h3>

You can add load balancing with [HAProxy](http://www.haproxy.org/) load-balancing software to support multiple API servers. In this setup, HAProxy provides access to the MAAS web UI and API.

[note]
If you happen to have Apache running on the same server where you intend to install HAProxy, you will need to stop and disable `apache2`, because HAProxy binds to port 80.
[/note]

<h4 id="heading--install">Install</h4>

``` bash
sudo apt install haproxy
```

<h4 id="heading--configure">Configure</h4>

Configure each API server's load balancer by copying the following into `/etc/haproxy/haproxy.cfg` (see the [upstream configuration manual](http://cbonte.github.io/haproxy-dconv/1.6/configuration.html) as a reference). Replace $PRIMARY_API_SERVER_IP and $SECONDARY_API_SERVER_IP with their respective IP addresses:

``` yaml
frontend maas
    bind    *:80
    retries 3
    option  redispatch
    option  http-server-close
    default_backend maas

backend maas
    timeout server 90s
    balance source
    hash-type consistent
    server localhost localhost:5240 check
    server maas-api-1 $PRIMARY_API_SERVER_IP:5240 check
    server maas-api-2 $SECONDARY_API_SERVER_IP:5240 check
```

where `maas-api-1` and `maas-api-2` are arbitrary server labels.

Now restart the load balancer to have these changes take effect:

``` bash
sudo systemctl restart haproxy
```

The configuration of region controller HA is now complete.

**The API server(s) must be now be referenced (e.g. web UI, MAAS CLI) using port 80 (as opposed to port 5240).**

<h2 id="heading--snap">Enable HA in a snap</h2>

Setting up high-availability using snaps is relatively easy. To use snaps instead of a package distribution of MAAS:

1.  Set up PostgreSQL for high-availability as [explained above](/t/high-availability/804#heading--postgresql-ha). PostgreSQL should run outside of the snap.
2.  [Install](/t/install-from-a-snap/773#heading--install-from-snap) the MAAS snap on each machine you intend to use as a rack or region controller. You'll need the MAAS shared secret, located here, `/var/lib/maas/secret`, on the first region controller you set up.
3.  [Initialise the snap](/t/install-from-a-snap/773#heading--initialisation) as a `rack` or `region` controller. Note that if you intend to use a machine as a region controller, you'll need to tell MAAS how to access your PostgreSQL database host with the following arguments:
    -   `--database-host DATABASE_HOST`
    -   `--database-name DATABASE_NAME`
    -   `--database-user DATABASE_USER`
    -   `--database-pass DATABASE_PASS`

<!-- LINKS -->