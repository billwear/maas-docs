Support for IPv6 in MAAS is similar to support for IPv4.  A rack controller in an IPv6 context needs to have the region API server URL specified with brackets:

``` nohighlight
http://[::1]:5240/MAAS/
```

You can access the [Web UI](/t/web-ui/782) and the [MAAS CLI](/t/maas-cli/802) (logging in to the API server) in the same way on both IPv4 and IPv6. To use an IPv6 address in a URL, surround it with square brackets. For example, on the local machine (`::1`, the IPv6 equivalent of `localhost`):

[note]
MAAS can only control most BMCs using IPv4.
[/note]

#### Quick questions you may have:

* [What should I know about enabling IPv6?](/t/ipv6-addressing/761#heading--enable-ipv6)
* [How do I configure an IPv6 subnet?](/t/ipv6-addressing/761#heading--configure-an-ipv6-subnet)
* [What should I know about routing?](/t/ipv6-addressing/761#heading--routing)

<h2 id="heading--enable-ipv6">Enable IPv6</h2>

You enable IPv6 networking in the same way that you enable IPv4 networking: configure a separate rack controller interface for your IPv6 subnet. The IPv6 interface must define a static address range. Provided that you already have a functioning IPv6 network, that's all there is to it. The following sections explain requirements, supported operations, and what to do if you don't yet have a functioning IPv6 network.

An IPv6 interface can use the same network interface on the rack controller as an existing IPv4 network interface. It just defines a different subnet, with IPv6 addressing. A machine that's connected to the IPv4 subnet also connected to the IPv6 subnet on the same network segment.

<h3 id="heading--configure-an-ipv6-subnet">Configure an IPv6 subnet</h3>

Define a reserved static IP range and machines deployed on the subnet will get a static address in this range. Since IPv6 networks usually are 64 bits wide you can be generous with the range size. Leave the netmask and broadcast address fields blank.

You may want MAAS to manage DHCP and DNS, but it's not required. Machines do not need a DHCP server at all for IPv6; MAAS configures static IPv6 addresses on a machine's network interface while deploying it. A DHCPv6 server can provide addresses for containers or virtual machines running on the machines, as well as devices on the network that are not managed by MAAS. The machines do not need DHCPv6. MAAS will not be aware of any addresses issued by DHCP, and cannot guarantee that they will stay unchanged.

<h3 id="heading--routing">Routing</h3>

In IPv6, clients do not discover routes through DHCP. Routers make themselves known on their networks by sending out route advertisements. These RAs also contain other configuration items:

 * Switches that allow stateless configuration of their unique IP addresses, based on MAC addresses. 
* Switches that enable them to request stateless configuration from a DHCP server.
* Switches that In any allow them to request a stateful IP address from a DHCP server. 

Since a network interface can have any number of IPv6 addresses even on a single subnet, several of these address assignment mechanisms can be combined.

However, when MAAS configures IPv6 networking on a machine, it does not rely on RAs. It statically configures a machine's default IPv6 route to use the router that is set on the cluster interface, so that the machine will know their default gateway. They do not need DHCP and will not autoconfigure global addresses.

You may be planning to operate DHCPv6 clients as well, for example, on machines not managed by MAAS, or on virtual machines hosted by MAAS machines.  If this is the case, you may want to configure RAs, so that those clients obtain configuration over DHCP.

If you need RAs but your gateway does not send them, install and configure `radvd` somewhere on the network to advertise its route.

<!-- LEAVE THIS OUT FOR NOW
<h3 id="heading--other-installers-and-operating-systems">Other installers and operating systems</h3>

Static IPv6 addresses are currently only configured on Ubuntu when installed using the "fast" installer. Other operating systems, including Ubuntu with the classic Debian installer, will not have their IPv6 addresses configured. The
same applies when a user manually installs an operating system on a machine or
overwrites its networking configuration: the machine will no longer have its
static IPv6 address configured, even if MAAS has allocated it to the machine.

However, as long as the address remains allocated to the machine, you may still
configure its operating system to use that address. The machine can then use that
address as if MAAS had configured it.
-->
<!-- LEAVE THIS OUT FOR NOW. I DO NOT SEE THIS OPTION IN THE WEB UI ANYWAY.
<h2 id="heading--disable-ipv4">Disable IPv4</h2>

For advanced users, there is an experimental capability to deploy machines with
pure IPv6, with IPv4 networking disabled. To enable this on a machine, check the
"Disable IPv4 when deployed" box on the machine's Edit page. The process of
managing and deploying the machine will still mostly work through IPv4, but once
deployed, the machine will have IPv6 networking only.

In practice, machines may not be functional without IPv4 networking.  A few things are known to be needed in any case:
-->
<!-- LINKS -->