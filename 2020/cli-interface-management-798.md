The following document provides a list of interface management tasks which can be performed by the MAAS CLI.

#### Quick questions you may have:

* [How do I use the MAAS CLI?](/t/maas-cli/802)
* [What might I need to know about networking?](/t/networking/768)
* [How do I commission machines?](/t/commission-machines/822)
* [How do I discover interface identifiers?](/t/cli-interface-management/798#heading--interface-identifiers)
* [How do I create a bond interface?](/t/cli-interface-management/798#heading--create-a-bond-interface)
* [How do I create a bridge interface?](/t/cli-interface-management/798#heading--create-a-bridge-interface)
* [How do I delete an interface?](/t/cli-interface-management/798#heading--delete-an-interface)
* [How do I create a VLAN interface?](/t/cli-interface-management/798#heading--create-a-vlan-interface)
* [How do I delete a VLAN interface?](/t/cli-interface-management/798#heading--delete-a-vlan-interface)

<h2 id="heading--interface-identifiers">Interface identifiers</h2>

The MAAS CLI uses a numeric interface identifier for many interface operations. Use the following command to retrieve the identifier(s):

``` bash
maas $PROFILE interfaces read $SYSTEM_ID
```

Look for either id or the number at the end of an interface's resource URI, such as **15** in the following example output:

``` json
"id": 15,
"mac_address": "52:54:00:55:06:40",
...
"name": "ens9",
...
"resource_uri": "/MAAS/api/2.0/nodes/4efwb4/interfaces/15/"
```

<h2 id="heading--create-a-bond-interface">Create a Bond interface</h2>

A bond interface is used to aggregate two or more more physical interfaces into a single logical interface. A bond can be created with the following command:

``` bash
maas $PROFILE interfaces create-bond $SYSTEM_ID name=$BOND_NAME \
parents=$IFACE1_ID mac_address=$MAC_ADDR \ 
parents=$IFACE2_ID bond_mode=$BOND_MODE \
bond_updelay=$BOND_UP bond_downdelay=$BOND_DOWN mtu=$MTU
```

Use the 'parents' parameters to define which interfaces form the aggregate interface.

The 'bond_updelay' and 'bond_downdelay' parameters specify the number of milliseconds to wait before either enabling or disabling a slave after a failure has been detected.

The following is an example of 'create-bond' in action:

``` bash
maas admin interfaces create-bond 4efwb4 name=bond0 parents=4 \
mac_address=52:52:00:00:00:00 parents=15 bond_mode=802.3ad \
bond_updelay=200 bond_downdelay=200 mtu=9000
```

See [Bond interfaces](/t/commission-machines/822#heading--bond-interfaces) for details on supported bond modes and their actions.

<h2 id="heading--create-a-bridge-interface">Create a Bridge interface</h2>

A bridge interface is created with the following syntax:

``` bash
maas $PROFILE interfaces create-bridge $SYSTEM_ID name=$BRIDGE_NAME \
parent=$IFACE_ID
```

Use 'parent' to define the primary interface used for the bridge:

``` bash
maas admin interfaces create-bridge 4efwb4 name=bridged0 parent=4
```

<h2 id="heading--delete-an-interface">Delete an interface</h2>

The 'delete' command can be used to delete a bridge interface, a bond interface or a physical interface:

``` bash
maas $PROFILE interface delete $SYSTEM_ID $IFACE_ID
```

For example:

``` bash
maas admin interface delete 4efwb4 15
```

The following is output after the successful deletion of an interface:

``` no-highlight
Success.
Machine-readable output follows:
```

[note]
There is no machine-readable output after the successful execution of the delete command.
[/note]

<h2 id="heading--create-a-vlan-interface">Create a VLAN interface</h2>

To create a VLAN interface, use the following syntax:

``` bash
maas $PROFILE vlans create $FABRIC_ID name=$NAME vid=$VLAN_ID
```

For example, the following command creates a VLAN called 'Storage network:

``` bash
maas admin vlans create 0 name="Storage network" vid=100
```

The above command generates the following output:

``` no-output
Success.
Machine-readable output follows:
{
    "vid": 100,
    "mtu": 1500,
    "dhcp_on": false,
    "external_dhcp": null,
    "relay_vlan": null,
    "name": "Storage network",
    "space": "undefined",
    "fabric": "fabric-0",
    "id": 5004,
    "primary_rack": null,
    "fabric_id": 0,
    "secondary_rack": null,
    "resource_uri": "/MAAS/api/2.0/vlans/5004/"
}
```

Be aware that the $VLAN_ID parameter does not indicate a VLAN ID that corresponds to the VLAN tag. You must first create the VLAN and then associate it with the interface:

``` bash
maas $PROFILE interfaces create-vlan $SYSTEM_ID vlan=$OUTPUT_VLAN_ID \
parent=$IFACE_ID
```

[note]
**OUTPUT_VLAN_ID** corresponds to the id value output when MAAS created the VLAN.
[/note]

The following example contains values that correspond to the output above:

``` bash
maas admin interfaces create-vlan 4efwb4 vlan=5004 parent=4
```

The above command generates the following output:

``` json
Success.
Machine-readable output follows:
{
    "tags": [],
    "type": "vlan",
    "enabled": true,
    "system_id": "4efwb4",
    "id": 21,
    "children": [],
    "mac_address": "52:54:00:eb:f2:29",
    "params": {},
    "vlan": {
        "vid": 100,
        "mtu": 1500,
        "dhcp_on": false,
        "external_dhcp": null,
        "relay_vlan": null,
        "id": 5004,
        "secondary_rack": null,
        "fabric_id": 0,
        "space": "undefined",
        "fabric": "fabric-0",
        "name": "Storage network",
        "primary_rack": null,
        "resource_uri": "/MAAS/api/2.0/vlans/5004/"
    },
    "parents": [
        "ens3"
    ],
    "effective_mtu": 1500,
    "links": [
        {
            "id": 55,
            "mode": "link_up"
        }
    ],
    "discovered": null,
    "name": "ens3.100",
    "resource_uri": "/MAAS/api/2.0/nodes/4efwb4/interfaces/21/"
}
```

<h2 id="heading--delete-a-vlan-interface">Delete a VLAN interface</h2>

The following command outlines the syntax required to delete a VLAN interface from the command line:

``` bash
maas $PROFILE vlan delete $FABRIC__ID $VLAN_ID
```

Using the values from previous examples, you executed this step as follows:

``` bash
maas admin vlan delete 0 100
```

<!-- LINKS -->