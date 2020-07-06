You can use the Web UI to manage the networking elements of MAAS, including subnets, fabrics, VLANs, and spaces.  This page shows how to access and edit these elements. See [Concepts and terms](/t/concepts-and-terms/785) for the definitions of networking objects, along with a basic networking tutorial (if needed).

<h2 id="heading--main-view">Main view</h2>

To access the main networking view visit the 'Subnets' page:

![subnets page](https://assets.ubuntu.com/v1/657bb332-installconfig-networking__2.4_subnets.png)

Due to the nature of this example, we re-use elements like fabrics, VLANs, subnets, and spaces.  MAAS can automatically detect these elements -- here there are two fabrics, one VLAN, five subnets, and two spaces -- but if MAAS doesn't find them, they can be added manually using the 'Add' button.

This main view can also be filtered either by fabrics or by spaces through the use of the 'Group by' dropdown.

Fabrics, VLANs, and spaces do not require much configuration beyond names and descriptions. You can change the MTU for a VLAN, as well as [enable DHCP](/t/dhcp/759#heading--enabling-dhcp) (see 'Take action' button).  None of these options requires detailed instruction. A subnet, on the other hand, provides a number of configuration options relevant to the day-to-day operation of MAAS.

<h3 id="heading--subnet-window">Subnet window</h3>

Clicking a subnet (here `192.168.100.0/24`) will display its detail screen, which presents several vertical sections.  Let's take them one by one.

<h4>Subnet summary</h4>

The **Subnet summary** section is the largest and most complex of the subnet configuration screens:

![networking subnets page summary](https://assets.ubuntu.com/v1/17617b35-installconfig-networking__2.4_subnets-summary.png)

This screen presents the following configurable options:

* **Name**: Subnet names can be any valid text string, although usually, and by default, they are named with the CIDR of the subnet itself.

* **CIDR**: This is the address parameter for the subnet.  In keeping with standard CIDR notation, the number of bits of the prefix are indicated after the slash.

* **Gateway IP**: This is the address of the default gateway for your subnet, which is the IP address that transfers packets to other subnets or networks. Typically, this is simply the first IP address in a block of addresses (the `.1` address).

* **DNS**: This is the address of a DNS (domain name server, or simply "nameserver") for your subnet.  It's optional, but can be configured if desired.

* **Description**: This field represents freeform text that you can enter to describe your subnet, as needed to keep important notes attached to the definition of the subnet.

* **Managed allocation** refers to the ability of MAAS to completely manage a subnet. See [Subnet management](/t/subnet-management/766).

* **Active mapping** instructs MAAS to scan the subnet every 3 hours to discover hosts that have not been discovered passively.

* **Proxy access** instructs MAAS to allow clients from this subnet to access the MAAS proxy.

* **Allow DNS resolution** allows subnet clients to use MAAS for DNS resolution.

* **Fabric**: This field allows you to set the subnet's fabric.

* **VLAN**: This field allows you to set the subnet's VLAN.

* **Space** is presented for clarity, though spaces are managed at the VLAN level.

<h4>Utilisation</h4>

This section of the subnet page presents metrics regarding address usage by this subnet.

![networking subnets utilisation](https://assets.ubuntu.com/v1/3d3e4b61-installconfig-networking__2.4_subnets-utilisation.png)

'Subnet addresses' shows the total number of addresses associated with the subnet, here 254. 'Availability' shows how many of those addresses are unused, and therefore "available", here 189, which corresponds to a percentage of roughly 74% of the total. Finally, 'Used' shows the percentage that is used, here roughly 26%.

<h4>Static Routes</h4>

This section can be used to define a static route between two subnets. A route is defined on a per-subnet basis to use a particular gateway, using a configured destination and metric.

To create a static route, click the 'Add static route' button to reveal the edit pane. Enter a Gateway IP address, select a destination subnet from the 'Destination' drop-down list, and edit the routing metric value if needed. Clicking 'Add' will activate the route. Routes can be edited and removed using the icons to the right of each entry.

![networking static routes configuration](https://assets.ubuntu.com/v1/49f5e240-installconfig-networking__2.4_subnets-routes.png)

<h4>Reserved ranges</h4>

The reserved ranges section of the subnet screen looks like this:

![networking subnets reserved](https://assets.ubuntu.com/v1/5f9f50f9-installconfig-networking__2.4_subnets-reserved.png)

This subject is treated separately in [IP ranges](/t/ip-ranges/760).

<h4>Used</h4>

This section displays hosts (including controllers) associated with the used addresses along with related bits of host information.

![networking subnets used](https://assets.ubuntu.com/v1/b9e69b0b-installconfig-networking__2.4_subnets-used.png)


<!-- LINKS -->