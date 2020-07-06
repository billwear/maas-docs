This page presents a list of common tasks that you can perform via the MAAS CLI. See [MAAS CLI](/t/maas-cli/802) for instructions to help get you started with the CLI.

#### Quick questions you may have:

* [How can I list nodes?](/t/common-cli-tasks/794#heading--list-nodes)
* [How can I determine a node system ID?](/t/common-cli-tasks/794#heading--determine-a-node-system-id)
* [How can I commission a node?](/t/common-cli-tasks/794#heading--commission-a-node)
* [How can I acquire a node?](/t/common-cli-tasks/794#heading--acquire-a-node)
* [How can I deploy a node?](/t/common-cli-tasks/794#heading--deploy-a-node)
* [How can I configure the deployment timeout?](/t/common-cli-tasks/794#heading--configure-deployment-timeout)
* [How can I control subnet management?](/t/common-cli-tasks/794#heading--control-subnet-management)
* [How can I create a reserved IP range?](/t/common-cli-tasks/794#heading--create-a-reserved-ip-range)
* [How can I determine a fabric ID?](/t/common-cli-tasks/794#heading--determine-a-fabric-id)
* [How can I enable DHCP?](/t/common-cli-tasks/794#heading--enable-dhcp)
* [How can I set a DNS forwarder?](/t/common-cli-tasks/794#heading--set-a-dns-forwarder)
* [How can I configure proxying?](/t/common-cli-tasks/794#heading--configure-proxying)
* [How can I set a default gateway?](/t/common-cli-tasks/794#heading--set-a-default-gateway)
* [How can I set a DNS server?](/t/common-cli-tasks/794#heading--set-a-dns-server)
* [How can I set a zone description?](/t/common-cli-tasks/794#heading--set-a-zone-description)
* [How can I add a public SSH key?](/t/common-cli-tasks/794#heading--add-a-public-ssh-key)
* [How can I determine a node hostname?](/t/common-cli-tasks/794#heading--determine-a-node-hostname)
* [How can I create a regular user?](/t/common-cli-tasks/794#heading--create-a-regular-user)

<h2 id="heading--list-nodes">List nodes</h2>
<p>To list all nodes (and their characteristics) in the MAAS:</p>

``` bash
maas $PROFILE nodes read
```

You can also specify various parameters to narrow your search. In the following example, MAAS will return any machines containing 'node2' in the `hostname` parameter.

``` bash
maas $PROFILE machines read hostname=node2
```

To see a list of all available search parameters:

``` bash
maas $PROFILE machines read --help
```

<h2 id="heading--determine-a-node-system-id">Determine a node system ID</h2>

You can uses  `jq` to determine a node's system ID. For example, here's how to output just the `hostname` and `system_id` when searching for a particular hostname:

``` bash
maas $PROFILE machines read | jq '.[] | .hostname, .system_id'
```

[jq](https://stedolan.github.io/jq/) is a command-line JSON processor.

The output looks like this:

``` no-highlight
"node2"
"e8xa8m"
```

<h2 id="heading--commission-a-node">Commission a node</h2>

To commission a node:

``` bash
maas $PROFILE machine commission $SYSTEM_ID
```

[note]
To commission a node, it must have a status of 'New'.
[/note]

To commission all nodes in the 'New' state:

``` bash
maas $PROFILE machines accept-all
```

See [Commission nodes](/t/commission-nodes/822).

<h2 id="heading--acquire-a-node">Acquire a node</h2>

To acquire/allocate a random node:

``` bash
maas $PROFILE machines allocate
```

To acquire/allocate a specific node:

``` bash
maas $PROFILE machines allocate system_id=$SYSTEM_ID
```

[note]
To acquire a node, it must have a status of 'Ready'.
[/note]

<h2 id="heading--deploy-a-node">Deploy a node</h2>

To deploy a node:

``` bash
maas $PROFILE machine deploy $SYSTEM_ID
```

To deploy a node as a KVM host:

``` bash
maas $PROFILE machine deploy $SYSTEM_ID install_kvm=True
```

[note]
To deploy with the CLI, the node must have a status of 'Allocated'. See 'Acquire a node' above (or use the [web UI](/t/deploy-nodes/825#acquire-nodes)).
[/note]

See [Deploy nodes](/t/deploy-nodes/825).

<h2 id="heading--configure-deployment-timeout">Configure deployment timeout</h2>

By default, when you deploy a node, MAAS will consider the deployment a failure if it doesn't complete within 30 minutes. However, this timeout is configurable:

``` bash
maas $PROFILE set-config name=node-timeout value=$NUMBER_OF_MINUTES
```


<h2 id="heading--control-subnet-management">Control subnet management</h2>

To enable or disable subnet management:

``` bash
maas $PROFILE subnet update $SUBNET_CIDR managed=false|true
```

For example, to disable:

``` bash
maas $PROFILE subnet update 192.168.1.0/24 managed=false
```

You can use the subnet's ID in place of the CIDR address.

See [Subnet management](/t/subnet-management/766).

<h2 id="heading--create-a-reserved-ip-range">Create a reserved IP range</h2>

See [Concepts and terms](/t/concepts-and-terms/785#heading--ip-ranges) for an explanation of the two kinds of reserved IP ranges MAAS uses.

To create a range of dynamic IP addresses that will be used by MAAS for node enlistment, commissioning, and possibly deployment:

``` bash
maas $PROFILE ipranges create type=dynamic \
    start_ip=$IP_DYNAMIC_RANGE_LOW end_ip=$IP_DYNAMIC_RANGE_HIGH \
    comment='This is a reserved dynamic range'
```

To create a range of IP addresses that will not be used by MAAS:

``` bash
maas $PROFILE ipranges create type=reserved \
    start_ip=$IP_STATIC_RANGE_LOW end_ip=$IP_STATIC_RANGE_HIGH \
    comment='This is a reserved range'
```

To reserve a single IP address that will not be used by MAAS:

``` bash
maas $PROFILE ipaddresses reserve ip_address=$IP_STATIC_SINGLE
```

To remove such a single reserved IP address:

``` bash
maas $PROFILE ipaddresses release ip=$IP_STATIC_SINGLE
```

<h2 id="heading--determine-a-fabric-id">Determine a fabric ID</h2>

To determine a fabric ID based on a subnet address:

``` bash
FABRIC_ID=$(maas $PROFILE subnet read $SUBNET_CIDR \
    | grep fabric | cut -d ' ' -f 10 | cut -d '"' -f 2)
```

<h2 id="heading--enable-dhcp">Enable DHCP</h2>

To enable DHCP on a VLAN on a certain fabric:

``` bash
maas $PROFILE vlan update $FABRIC_ID $VLAN_TAG dhcp_on=True \
    primary_rack=$PRIMARY_RACK_CONTROLLER
```

To enable DHCP HA, you will need both a primary and a secondary controller:

``` bash
maas $PROFILE vlan update $FABRIC_ID $VLAN_TAG dhcp_on=True \
    primary_rack=$PRIMARY_RACK_CONTROLLER \
    secondary_rack=$SECONDARY_RACK_CONTROLLER 
```

You will also need to set a default gateway (see [below](#heading--set-a-default-gateway)).

[note]
You must enable DHCP for PXE booting on the 'untagged' VLAN.
[/note]

See [DHCP](/t/dhcp/759) for more on this subject.

<h2 id="heading--set-a-dns-forwarder">Set a DNS forwarder</h2>

To set a DNS forwarder:

``` bash
maas $PROFILE maas set-config name=upstream_dns value=$MY_UPSTREAM_DNS
```

<h2 id="heading--configure-proxying">Configure proxying</h2>

Enabling and disabling proxying, in general, is done via a boolean option ('true' or 'false'). The following command will disable proxying completely:

``` bash
maas $PROFILE maas set-config name=enable_http_proxy value=false
```

To set an external proxy, ensure proxying is enabled (see above) and then define it:

``` bash
maas $PROFILE maas set-config name=http_proxy value=$EXTERNAL_PROXY
```

For example,

``` bash
maas $PROFILE maas set-config name=enable_http_proxy value=true
maas $PROFILE maas set-config name=http_proxy value=http://squid.example.com:3128/
```

Enabling and disabling proxying per subnet is done via a boolean option ('true' or 'false'). Here is how you can disable proxying on a per-subnet basis:

``` bash
maas $PROFILE subnet update $SUBNET_CIDR allow_proxy=false
```

For example,

``` bash
maas $PROFILE subnet update 192.168.0.0/22 allow_proxy=false
```

See [Proxy](/t/proxy/763) for detailed information on how proxying works with MAAS.

<h2 id="heading--set-a-default-gateway">Set a default gateway</h2>

To set the default gateway for a subnet:

``` bash
maas $PROFILE subnet update $SUBNET_CIDR gateway_ip=$MY_GATEWAY
```

<h2 id="heading--set-a-dns-server">Set a DNS server</h2>

To set the DNS server for a subnet:

``` bash
maas $PROFILE subnet update $SUBNET_CIDR dns_servers=$MY_NAMESERVER
```

<h2 id="heading--set-a-zone-description">Set a zone description</h2>

To set a description for a physical zone:

``` bash
maas $PROFILE zone update default \
    description="This zone was configured by a script."
```

See [Zones](/t/availability-zones/820) for more information on this topic.

<h2 id="heading--add-a-public-ssh-key">Add a public SSH key</h2>

To add a public SSH key to a MAAS user account:

``` bash
maas $PROFILE sshkeys create "key=$SSH_KEY"
```

See [SSH keys](/t/user-accounts/790#heading--ssh-keys).

<h2 id="heading--determine-a-node-hostname">Determine a node hostname</h2>

To determine a node's hostname based on it's MAC address:

``` bash
HOSTNAME=$(maas $PROFILE nodes read mac_address=$MAC \
    | grep hostname | cut -d '"' -f 4)
```

<h2 id="heading--create-a-regular-user">Create a regular user</h2>

To create a regular user:

``` bash
maas $PROFILE users create username=$USERNAME \
    email=$EMAIL_ADDRESS password=$PASSWORD is_superuser=0
```

All the options are necessary. Note that stipulating a password on the CLI may be a security hazard, depending on your environment. If unsure, use the web UI. See [User Accounts](/t/user-accounts/790) for the latter.

<!-- LINKS -->