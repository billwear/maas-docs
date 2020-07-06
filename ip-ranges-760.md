In MAAS-managed networks, you can further manage your subnets with a reserved range of IP addresses.  You can reserve IP addresses by adding one or more reserved ranges to a subnet configuration. You can define two types of ranges: reserved ranges and reserved dynamic ranges.  

A reserved range operates differently depending on whether the subnet is managed or unmanaged.  For a managed (subnet), MAAS will never assign IP addresses inside this range.  You can use this range for anything, such as infrastructure systems, network hardware, external DHCP, or an OpenStack namespace.  For an unmanaged (subnet), MAAS will only assign IP addresses inside this range -- but MAAS can assign any IP within this range.

A reserved dynamic range is used by MAAS for enlisting, commissioning and, if enabled, MAAS-managed DHCP on the machine's VLAN during commissioning and deployment. If created with the Web UI, an initial range is created as part of the DHCP enablement process. MAAS never uses IP addresses from this range for an unmanaged subnet.

This article gives specific instructions about creating and managing IP ranges.

#### Quick questions you may have:

* [How do I create an IP range?](/t/ip-ranges/760#heading--create-a-range)
* [How do I edit an existing IP range?](/t/ip-ranges/760#heading--edit-a-range)
* [How do I delete an existing IP range?](/t/ip-ranges/760#heading--delete-a-range)

<h2 id="heading--create-a-range">Create a range</h2>

Choose 'Reserve range' or 'Reserve dynamic range'. If you choose the latter, MAAS will automatically provide DHCP for enlistment and commissioning provided that the associated VLAN has DHCP enabled. Read the [DHCP page](/t/dhcp/759).

When you choose either of those two options, a window will appear allowing you to enter start and end addresses for the range as well as a comment.

Below is an example window when creating a 'reserved range' (the windows are identical):

![reserved IP range](https://assets.ubuntu.com/v1/be85b7d6-installconfig-network-ipranges__2.4_add-reserved-iprange.png)

Click the 'Reserve' button when done.

See [MAAS CLI](/t/common-cli-tasks/794#heading--create-a-reserved-ip-range) for doing this with the CLI.

<h2 id="heading--edit-a-range">Edit a range</h2>

Click the 'Menu' button at the far right of the row corresponding to the subnet in question and select 'Edit reserved range' from the menu that appears. Edit the fields as desired and click the 'Save' button.

<h2 id="heading--delete-a-range">Delete a range</h2>

Select 'Remove range' from the menu that appears when clicking the 'Menu' button at the far right of the row corresponding to the subnet in question.

<!-- LINKS -->