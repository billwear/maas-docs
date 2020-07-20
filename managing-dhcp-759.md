MAAS enlists and commissions machines through the use of its DHCP server running on an untagged VLAN. Although this MAAS-managed DHCP can also be part of the deploy phase, an external DHCP server can optionally be used instead for this purpose. If MAAS detects an external DHCP server, it will display it on the rack controller's page, accessible by selecting 'Controllers' from the top menu in the web UI.

In addition, the machine subnet is usually on the untagged VLAN. If not, you will need to route DHCP packets between the subnet and the MAAS-provided DHCP subnet. It is also possible to forward DHCP traffic from one VLAN to another using an external DHCP relay service.

This documentation presupposes that MAAS-managed DHCP is used to enlist and commission machines.  Using an external DHCP server for enlistment and commissioning may work, but note that this is not supported. MAAS cannot manage an external DHCP server, nor can it keep leases synchronised when you return a machine to the pool.

This article delves into these issues and offers guidance on how to set up and manage your DHCP configuration.

#### Quick questions you may have: 

* [What are reserved IP ranges?](/t/concepts-and-terms/785#heading--ip-ranges)
* [What is post-commission configuration](/t/commission-nodes/822#heading--post-commission-configuration)
* [How and why should I enable MAAS-managed DHCP?](/t/managing-dhcp/759#heading--enabling-dhcp)
* [How do I resolve IP conflicts?](/t/managing-dhcp/759#heading--resolving-ip-conflicts)
* [How can I extend a reserved dynamic IP range?](/t/managing-dhcp/759#heading--extending-a-reserved-dynamic-ip-range)
* [How and why should I configure external  DHCP?](/t/managing-dhcp/759#heading--external-dhcp-and-a-reserved-ip-range)
* [How and why should I use a DHCP relay?](/t/managing-dhcp/759#heading--dhcp-relay)
* [How and why should I customize MAAS with DHCP snippets?](/t/managing-dhcp/759#heading--dhcp-snippets)

<h2 id="heading--enabling-dhcp">Enabling DHCP</h2>

MAAS-managed DHCP needs a reserved dynamic IP range enlist and commission machines. You should create such a range when you are enabling DHCP with the web UI. See [MAAS CLI](/t/common-cli-tasks/794#heading--enable-dhcp) for instructions for doing this with the CLI.

To enable MAAS-managed DHCP, under the 'Subnets' page select the desired VLAN and then:

1.  Under the 'Take action' button select 'Provide DHCP'. A new window will appear.
2.  Select the primary rack controller. For DHCP HA, select both the primary and the secondary.
3.  Create a reserved, dynamic IP range. Fill in the fields 'Dynamic range start IP' and 'Dynamic range end IP'.
4.  Apply your changes with the 'Provide DHCP' button.

![Enable DHCP](upload://eIybeaIL1tqr4WWfff94DjMy0Wr.png)

Now, addresses in this range will get assigned to machines that are being either enlisted or commissioned.  In addition, if you are deploying a machine that has an interface connected to the untagged VLAN, and it has an IP assignment mode set to 'DHCP,' then it will also get an address in this range.

<h3 id="heading--resolving-ip-conflicts">Resolving IP conflicts</h3>

In some cases, MAAS manages a subnet that is not empty, which could result in MAAS assigning a duplicate IP address.  For users of MAAS versions below 2.7, you need to tell MAAS about IP addresses that are already assigned on that subnet to avoid an IP conflict.  MAAS version 2.7 is capable of detecting IPs in use on a subnet.  Be aware that there are a couple of caveats:

* If a previously-assigned NIC is in a quiescent state or turned off, MAAS may not detect it before duplicating an IP address.

* At least one rack controller must have access to the IP-assigned machine in order for this feature to work.

MAAS 2.7 also recognizes when the subnet ARP cache is full, so that it can re-check the oldest IPs added to the cache to search for free IP addresses.

<h2 id="heading--extending-a-reserved-dynamic-ip-range">Extending a reserved dynamic IP range</h2>

If necessary, it is possible to add further portions of the subnet to the dynamic IP range (see [IP ranges](/t/ip-ranges/760)). Furthermore, since you enabled DHCP on a VLAN basis and a VLAN can contain multiple subnets, it is possible to add a portion from those subnets as well. Just select the subnet under the 'Subnets' page and reserve a dynamic range. DHCP will be enabled automatically.

<h2 id="heading--external-dhcp-and-a-reserved-ip-range">External DHCP and a reserved IP range</h2>

If an external DHCP server is used to deploy machines, then a reserved IP range should be created to prevent the address namespace from being corrupted. For instance, address conflicts may occur if you set a machine's IP assignment mode to 'Auto assign' in the context of an external DHCP server. See [IP ranges](/t/ip-ranges/760) to create such a range. It should correspond to the lease range of the external server.

<h2 id="heading--dhcp-relay">DHCP relay</h2>

You should not enable DHCP relays in MAAS without sufficient planning.  In particular, MAAS does not provide the actual relay. It must be set up as an external service by the administrator. What MAAS does provide is the DHCP configuration that MAAS-managed DHCP requires in order to satisfy any client requests relayed from another VLAN.

To relay from one VLAN (source) to another VLAN (target):

1.  Ensure the target VLAN has DHCP enabled.

2.  Set up the external relay. This relay is set up independently from MAAS. See [DHCP relay](/t/concepts-and-terms/785#heading--dhcp-relay) for software suggestions.

3.  Configure MAAS-managed DHCP. Navigate to the source VLAN page and select the 'Relay DHCP' action. Fill in the fields in the resulting form. The crucial setting is the target VLAN ('Relay VLAN'). Press the 'Relay DHCP' button to finish. See [MAAS CLI](/t/advanced-cli-tasks/793#heading--relay-dhcp) for how to do this with the CLI.

<h2 id="heading--dhcp-snippets">DHCP Snippets</h2>

When MAAS manages DHCP, you customise it through the use of DHCP snippets. These are user-defined configuration options that can be applied either globally, per subnet, or per machine. You apply a global snippet to all VLANs, subnets, and machines. All three types end up in `/var/lib/maas/dhcpd.conf` or `/var/lib/maas/dhcpd6.conf`. For information on what options to use, refer to the [`dhcpd.conf` man page](http://manpages.ubuntu.com/cgi-bin/search.py?q=dhcpd.conf).

[note]
Modifications made directly to `dhcpd.conf.template` or `dhcpd6.conf.template` are not supported.
[/note]

To manage snippets, as an admin, open the 'Settings' page and click on the 'DHCP snippets' tab.

For example, to create a new snippet press 'Add custom snippet'. In the resulting window, choose a name and type for it and enter its associated DHCP configuration. Click 'Save snippet' to apply the change, and make sure to activate the checkbox in the 'Enabled' column of the snippets list.

![Manage DHCP snippets](upload://nhdZVwRlsH2xs1nI4PsOqMzIXrN.png)

See [MAAS CLI](/t/cli-dhcp-snippet-management/796) for doing this with the CLI.