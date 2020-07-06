This article is a list of advanced tasks you can perform with the MAAS CLI.

#### Quick questions you may have:

* [How do I get started with the MAAS CLI?](/t/maas-cli/802)
* [How do I edit machine hostname and power parameters?](/t/cli-advanced-tasks/793#heading--edit-node-hostname-and-power-parameters)
* [How do I relay DHCP?](/t/cli-advanced-tasks/793#heading--relay-dhcp)
* [How do I assign a network interface to a fabric?](/t/cli-advanced-tasks/793#heading--assign-a-network-interface-to-a-fabric)
* [How do I change the IP assignment mode of a network interface?](/t/cli-advanced-tasks/793#heading--change-the-ip-assignment-mode-of-a-network-interface)
* [How do I install a rack controller?](/t/cli-advanced-tasks/793#heading--install-a-rack-controller)
* [How do I list rack controllers?](/t/cli-advanced-tasks/793#heading--list-rack-controllers)
* [How do I set the default storage layout?](/t/cli-advanced-tasks/793#heading--set-the-default-storage-layout)
* [How do I set a storage layout?](/t/cli-advanced-tasks/793#heading--set-a-storage-layout)
* [How do I create an A or AAAA record in DNS?](/t/cli-advanced-tasks/793#heading--create-an-a-or-aaaa-record-in-dns)
* [How do I create an alias (CNAME) record in DNS?](/t/cli-advanced-tasks/793#heading--create-an-alias-cname-record-in-dns)
* [How do I create a Mail Exchange pointer record in DNS?](/t/cli-advanced-tasks/793#heading--create-a-mail-exchange-pointer-record-in-dns)
* [How do I add or update a remote syslog server?](/t/cli-advanced-tasks/793#heading--add-or-update-a-remote-syslog-server)

<h2 id="heading--edit-node-hostname-and-power-parameters">Edit machine hostname and power parameters</h2>

To edit the hostname and power parameters of a machine:

``` bash
maas $PROFILE machine update $SYSTEM_ID \
    hostname=$HOSTNAME \
    power_type=$POWER_TYPE \
    power_parameters_power_address=$POWER_ADDRESS \
    power_parameters_power_id=$HOSTNAME
```

For example, to configure a KVM-based machine:

``` bash
maas $PROFILE machine update $SYSTEM_ID \
    hostname=$HOSTNAME \
    power_type=virsh \
    power_parameters_power_address=qemu+ssh://ubuntu@$KVM_HOST/system \
    power_parameters_power_id=$HOSTNAME
```

See [Common CLI tasks](/t/common-cli-tasks/794#heading--determine-a-node-system-id) for how to find a machine's system id and [BMC Power Types](/t/bmc-power-types/830) for details on different power types.

<h2 id="heading--relay-dhcp">Relay DHCP</h2>

To relay DHCP traffic for a VLAN (source) through another VLAN (target):

``` bash
maas $PROFILE vlan update $FABRIC_ID $VLAN_VID_SRC relay_vlan=$VLAN_ID_TARGET
```

For example, to relay VLAN with vid 0 (on fabric-2) through VLAN with id 5002 :

``` bash
maas $PROFILE vlan update 2 0 relay_van=5002
```

See [DHCP relay](/t/dhcp/759#heading--dhcp-relay) for more information.

<h2 id="heading--assign-a-network-interface-to-a-fabric">Assign a network interface to a fabric</h2>

This task is made easier with the aid of the `jq` utility. It filters the `maas` command (JSON formatted) output and prints it in the desired way, which allows you to view and compare data quickly. Go ahead and install it:

``` bash
sudo apt install jq
```

In summary, MAAS assigns an interface to a fabric by assigning it to a VLAN. First, we need to gather various bits of data.

List some information on all machines:

``` bash
maas $PROFILE machines read | jq ".[] | \
    {hostname:.hostname, system_id: .system_id, status:.status}" --compact-output
```

Example output:

``` no-highlight
{"hostname":"machine1","system_id":"dfgnnd","status":4}
{"hostname":"machine2","system_id":"bkaf6e","status":6}
{"hostname":"machine4","system_id":"63wqky","status":6}
{"hostname":"machine3","system_id":"qwkmar","status":4}
```

[note]
You can only edit an interface when the corresponding machine has a status of 'Ready'. This state is numerically denoted by the integer '4'.
[/note]

List some information for all interfaces on the machine in question (identified by its system id 'dfgnnd'):

``` bash
maas $PROFILE interfaces read dfgnnd | jq ".[] | \
    {id:.id, name:.name, mac:.mac_address, vid:.vlan.vid, fabric:.vlan.fabric}" --compact-output
```

Example output:

``` no-highlight
{"id":8,"name":"eth0","mac":"52:54:00:01:01:01","vid":0,"fabric":"fabric-1"}
{"id":9,"name":"eth1","mac":"52:54:00:01:01:02","vid":null,"fabric":null}
```

List some information for all fabrics:

``` bash
maas $PROFILE fabrics read | jq ".[] | \
    {name:.name, vlans:.vlans[] | {id:.id, vid:.vid}}" --compact-output
```

Example output:

``` no-highlight
{"name":"fabric-0","vlans":{"id":5001,"vid":0}}
{"name":"fabric-1","vlans":{"id":5002,"vid":0}}
{"name":"fabric-2","vlans":{"id":5003,"vid":0}}
```

This example will show how to move interface '8' (on machine 'dfgnnd') from 'fabric-1' to 'fabric-0'. Based on the gathered information, this will consist of changing the interface's VLAN from '5002' to '5001':

``` bash
maas $PROFILE interface update dfgnnd 8 vlan=5001 >/dev/null
```

Verify the operation by relisting information for the machine's interface:

``` bash
maas $PROFILE interfaces read dfgnnd | jq ".[] | \
    {id:.id, name:.name, mac:.mac_address, vid:.vlan.vid, fabric:.vlan.fabric}" --compact-output
```

The output shows that the interface is now on fabric-0:

``` no-highlight
{"id":8,"name":"eth0","mac":"52:54:00:01:01:01","vid":0,"fabric":"fabric-0"}
{"id":9,"name":"eth1","mac":"52:54:00:01:01:02","vid":null,"fabric":null}
```

<h2 id="heading--change-the-ip-assignment-mode-of-a-network-interface">Change the IP assignment mode of a network interface</h2>

If you want to edit the IP assignment mode of a network interface, the existing subnet link first needs to be removed.

Begin by finding the interface ID as well as the interface's subnet link ID with the command:

``` bash
maas $PROFILE node read $SYSTEM_ID
```

Once that's done, proceed to unlink and link:

``` bash
maas $PROFILE interface unlink-subnet $SYSTEM_ID $INTERFACE_ID id=$SUBNET_LINK_ID
maas $PROFILE interface link-subnet $SYSTEM_ID $INTERFACE_ID mode=$IP_MODE subnet=$SUBNET_CIDR [$OPTIONS]
```

For instance, to have interface '58', with subnet link '146', on machine 'exqn37' use DHCP on subnet '192.168.1.0/24':

``` bash
maas $PROFILE interface unlink-subnet exqn37 58 id=146
maas $PROFILE interface link-subnet exqn37 58 mode=dhcp subnet=192.168.1.0/24
```

If instead of DHCP, you desire a static address, then the second command would look like this:

``` bash
maas $PROFILE interface link-subnet exqn37 58 mode=static subnet=192.168.1.0/24 ip_address=192.168.1.113
```

For a summary of IP assignment modes see [Post-commission configuration](/t/commission-nodes/822#heading--post-commission-configuration).

<h2 id="heading--install-a-rack-controller">Install a rack controller</h2>

To install and register a rack controller with the MAAS:

``` bash
sudo apt install maas-rack-controller
sudo maas-rack register
```

[note]
The register command is not required when you are adding a rack controller to a system that already houses an API server.
[/note]

MAAS will ask for the URL of the region API server. If you provide a hostname, ensure it is resolvable. Next, MAAS will prompt you for the secret key, stored in file `/var/lib/maas/secret` on the API server.

You can get the above information from the web UI by visiting the 'Controllers' page and clicking the button 'Add rack controller'. Here is an example of what you may see:

![cli-install-rackd](https://assets.ubuntu.com/v1/61fa1b99-manage-maas-cli-advanced__2.2_install-rackd.png)

Based on the above, then, we could have also entered:

``` bash
sudo maas-rack register --url http://10.5.1.5:5240/MAAS \
    --secret fa847000e7cb681101d26e3477e6e39e
```

See [Rack controller](/t/rack-controller/771) for an overview.

<h2 id="heading--list-rack-controllers">List rack controllers</h2>

To list all rack controllers registered with the region:

``` bash
maas $PROFILE rack-controllers read | grep hostname | cut -d '"' -f 4
```

<h2 id="heading--set-the-default-storage-layout">Set the default storage layout</h2>

To set the default storage layout for all machines:

``` bash
maas $PROFILE maas set-config name=default_storage_layout value=$LAYOUT_TYPE
```

For example, to set the default layout to Flat:

``` bash
maas $PROFILE maas set-config name=default_storage_layout value=flat
```

[note type="caution" status="Important"]
The new default will only apply to newly-commissioned machines.
[/note]

See [Storage](/t/storage/775) for more details on MAAS storage features.

<h2 id="heading--set-a-storage-layout">Set a storage layout</h2>

An administrator can set a storage layout for a machine with a status of 'Ready' like this:

``` bash
maas $PROFILE machine set-storage-layout $SYSTEM_ID storage_layout=$LAYOUT_TYPE [$OPTIONS]
```

For example, to set an LVM layout where the logical volume has a size of 5 GB:

``` bash
maas $PROFILE machine set-storage-layout $SYSTEM_ID storage_layout=lvm lv_size=5368709120
```

You must specify all storage sizes in bytes.

[note type="caution"]
This action will remove the configuration that may exist on any block device.
[/note]

<h2 id="heading--create-an-a-or-aaaa-record-in-dns">Create an A or AAAA record in DNS</h2>

An administrator can create an A record when creating a DNS resource with an IPv4 address.

``` bash
mass $PROFILE dnsresources create fqdn=$HOSTNAME.$DOMAIN ip_addresses=$IPV4ADDRESS
```

An administrator can create an AAAA record when creating a DNS resource with an IPv6 address.

``` bash
mass $PROFILE dnsresources create fqdn=$HOSTNAME.$DOMAIN ip_addresses=$IPV6ADDRESS
```

<h2 id="heading--create-an-alias-cname-record-in-dns">Create an alias (CNAME) record in DNS</h2>

An administrator can set a DNS Alias (CNAME record) to an already existing DNS entry of a machine.

``` bash
mass $PROFILE dnsresource-records create fqdn=$HOSTNAME.$DOMAIN rrtype=cname rrdata=$ALIAS
```

For example, to set webserver.maas.io to alias to www.maas.io:

``` bash
maas $PROFILE dnsresource-records create fqdn=webserver.maas.io rrtype=cname rrdata=www
```

<h2 id="heading--create-a-mail-exchange-pointer-record-in-dns">Create a Mail Exchange pointer record in DNS</h2>

An administrator can set a DNS Mail Exchange pointer record (MX and value) to a domain.

``` bash
maas $PROFILE dnsresource-records create fqdn=$DOMAIN rrtype=mx rrdata='10 $MAIL_SERVER.$DOMAIN'
```

For example, to set the domain.name managed by MAAS to have an MX record and that you own the domain:

``` bash
maas $PROFILE dnsresource-records create fqdn=maas.io rrtype=mx rrdata='10 smtp.maas.io'
```

<h2 id="heading--add-or-update-a-remote-syslog-server">Add or update a remote syslog server</h2>

To add or update a remote syslog server in your MAAS environment:

``` bash
maas $PROFILE maas set-config name="remote_syslog" value="$SYSLOG_FQDN"
```

For example, to set your syslog server to `192.168.100.11`:

``` bash
maas $PROFILE maas set-config name="remote_syslog" value=192.168.100.11
```

If you clear the `remote_syslog` value, MAAS will revert to the default behaviour, which is to send all syslog information to all MAAS region controllers.

For example:

``` bash
maas $PROFILE maas set-config name="remote_syslog" value=""
```

<!-- LINKS -->