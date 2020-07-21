This article provides a list of examples of VM host management tasks you can perform with the MAAS CLI.

#### Quick questions you may have: 

* [How do I get started with the MAAS CLI?](/t/maas-cli/802)
* [What should I know about VM hosts?](/t/introduction-to-vm-hosting/1524)
* [How can I add a VM host?](/t/cli-composable-hardware/795#heading--add-a-vm-host)
* [How can I find VM host IDs?](/t/cli-composable-hardware/795#heading--find-vm-host-ids)
* [How can I list resources of all VM hosts?](/t/cli-composable-hardware/795#heading--list-resources-of-all-vm-hosts)
* [How can I list resources of a VM host?](/t/cli-composable-hardware/795#heading--list-resources-of-a-vm-host)
* [How can I update a VM host's configuration?](/t/cli-composable-hardware/795#heading--update-vm-host-configuration)
* [How can I list a VM host's connection parameters?](/t/cli-composable-hardware/795#heading--list-vm-host-connection-parameters)
* [How can I compose VMs?](/t/cli-composable-hardware/795#heading--compose-virtual-machines)
* [How can I compose and allocate a VM?](/t/cli-composable-hardware/795#heading--compose-and-allocate-a-vm)
* [How can I list machine parameters?](/t/cli-composable-hardware/795#heading--list-machine-parameters)
* [How can I use libvirt storage pools?](/t/cli-composable-hardware/795#heading--libvirt-storage-pools)
* [How can I delete a VM?](/t/cli-composable-hardware/795#heading--delete-a-vm)
* [How can I delete a VM host?](/t/cli-composable-hardware/795#heading--delete-a-vm-host)

<h2 id="heading--add-a-vm-host">Add a VM host</h2>

To add a VM host:

``` bash
maas $PROFILE vm-hosts create type=$VM_HOST_TYPE power_address=$POWER_ADDRESS \
    [power_user=$USERNAME] [power_pass=$PASSWORD] [zone=$ZONE] \
    [tags=$TAG1,$TAG2,...]
```

[note]
Both USERNAME and PASSWORD are optional for the virsh power type. ZONE and TAGS are optional for all VM hosts.
[/note]

See the [API reference](/docs/api#power-types) for a listing of available power types.

For example, to create an RSD VM host:

``` bash
maas $PROFILE vm-hosts create type=rsd power_address=10.3.0.1:8443 \
    power_user=admin power_pass=admin
```

And to create a KVM host:

``` bash
maas $PROFILE vm-hosts create type=virsh power_address=qemu+ssh://ubuntu@192.168.1.2/system
```

Create a KVM host with [overcommitted resources](/t/add-a-vm-host/1523#heading--overcommit-resources):

``` bash
maas $PROFILE vm-hosts create type=virsh power_address=qemu+ssh://ubuntu@192.168.1.2/system \
        power_pass=example cpu_over_commit_ratio=0.3 memory_over_commit_ratio=4.6
```

Create a KVM host that uses a default [storage pool](/t/manage-composable-machines/812#heading--configuration):

``` bash
maas $PROFILE vm-hosts create type=virsh power_address=qemu+ssh://ubuntu@192.168.1.2/system \
        power_pass=example default_storage_pool=pool1
```

<h2 id="heading--find-vm-host-ids">Find VM host IDs</h2>

Here's a simple way to find a VM host's ID by name using `jq`:

``` bash
maas $PROFILE vm-hosts read | jq '.[] | select (.name=="MyVMHost") | .name, .id'
```

[note] `jq` is a command-line JSON processor. More details at https://stedolan.github.io/jq/[/note]

Example output:

``` no-highlight
"MyVMHost"
1
```

<h2 id="heading--list-resources-of-all-vm-hosts">List resources of all VM hosts</h2>

``` bash
maas $PROFILE vm-hosts read
```

A portion of the sample output:

``` no-highlight
        "id": 93,
        "capabilities": [
            "composable",
            "fixed_local_storage",
            "iscsi_storage"
        ],
        "name": "civil-hermit",
```

<h2 id="heading--list-resources-of-a-vm-host">List resources of a VM host</h2>

To list an individual VM host's resources:

``` bash
maas $PROFILE vm-host read $VM_HOST_ID
```

<h2 id="heading--update-vm-host-configuration">Update VM host configuration</h2>

Update overcommit ratios for a KVM host:

``` bash
maas $PROFILE vm-host update $VM_HOST_ID power_address=qemu+ssh://ubuntu@192.168.1.2/system \
        power_pass=example cpu_over_commit_ratio=2.5 memory_over_commit_ratio=10.0
```

Update the default storage pool used by a KVM host:

``` bash
maas $PROFILE vm-host update $VM_HOST_ID power_address=qemu+ssh://ubuntu@192.168.1.2/system \
        power_pass=example default_storage_pool=pool2
```

<h2 id="heading--list-vm-host-connection-parameters">List VM host connection parameters</h2>

To list a VM host's connection parameters:

``` bash
maas $PROFILE vm-host parameters $VM_HOST_ID
```

Example output:

``` no-highlight
{
    "power_address": "10.3.0.1:8443",
    "power_pass": "admin",
    "power_user": "admin"
}
```

<h2 id="heading--compose-virtual-machines">Compose VMs</h2>

<h3 id="heading--basic">Basic</h3>

To compose a basic VM:

``` bash
maas $PROFILE vm-host compose $VM_HOST_ID
```

Example output for default composing:

``` no-highlight
{
    "system_id": "73yxmc",
    "resource_uri": "/MAAS/api/2.0/machines/73yxmc/"
}
```

<h3 id="heading--set-resources">Set resources</h3>

Compose with resources specified:

``` bash
maas $PROFILE vm-host compose $VM_HOST_ID $RESOURCES
```

Where RESOURCES is a space-separated list from:

**cores=** requested cores
**cpu_speed=** requested minimum cpu speed in MHz
**memory=** requested memory in MB
**architecture=** See [Architecture](#heading--architecture) below
**storage=** See [Storage](#heading--storage) below
**interfaces=** See [Interfaces](#heading--interfaces) below

<h4 id="heading--architecture">Architecture</h4>

To list available architectures:

``` bash
maas $PROFILE boot-resources read
```

Then, for example:

``` bash
maas $PROFILE vm-host compose $VM_HOST_ID \
    cores=40 cpu_speed=2000 memory=7812 architecture="amd64/generic"
```

<h4 id="heading--storage">Storage</h4>

Storage parameters look like this:

``` no-highlight
storage="<label>:<size in GB>(<storage pool name>),<label>:<size in GB>(<storage pool name>)"
```

For example, to compose a machine with the following disks:

-   32 GB disk from storage pool `pool1`
-   64 GB disk from storage pool `pool2`

where you want to use the first as a bootable root partition `/` and the second to be used as a home directory.

First, create the VM:

``` bash
maas $PROFILE vm-host compose $VM_HOST_ID "storage=mylabel:32(pool1),mylabel:64(pool2)"
```

Note that the labels, here `mylabel`, are an ephemeral convenience that you might find useful in scripting MAAS actions.

MAAS will create a VM with 2 disks, `/dev/vda` (32 GB) and `/dev/vdb` (64 GB). After MAAS enlists, commissions and acquires the machine, you can edit the disks before deploying to suit your needs. For example, we'll set a boot, root and home partition.

We'll start by deleting the `/` partition MAAS created because we want a separate `/boot` partition to demonstrate how this might be done.

``` bash
maas admin partition delete $VM_HOST_ID $DISK1_ID $PARTITION_ID
```

[note]
To find `$DISK1_ID` and `$PARTITION_ID`, use `maas admin machine read $VM_HOST_ID`.
[/note]

Now, create a boot partition (~512MB):

``` bash
maas admin partitions create $VM_HOST_ID $DISK1_ID size=512000000 bootable=True
```

We'll use the remaining space for the root partition, so create another without specifying size:

``` bash
maas admin partitions create $VM_HOST_ID $DISK1_ID
```

Finally, create a partition to use as the home directory. Here we'll use the entire space:

``` bash
maas admin partitions create $VM_HOST_ID $DISK2_ID
```

[note]
To find `$DISK2_ID`, use `maas admin machine read $VM_HOST_ID`.
[/note]

Now, format the partitions. This requires three commands:

``` bash
maas admin partition format $VM_HOST_ID $DISK1_ID $BOOT_PARTITION_ID fstype=ext2
maas admin partition format $VM_HOST_ID $DISK1_ID $ROOT_PARTITION_ID fstype=ext4
maas admin partition format $VM_HOST_ID $DISK2_ID $HOME_PARTITION_ID fstype=ext4
```

[note]
To find the partition IDs, use `maas admin partitions read $VM_HOST_ID $DISK1_ID` and `maas admin partitions read $VM_HOST_ID $DISK2_ID`
[/note]

Before you can deploy the machine with our partition layout, you need to mount the new partitions. Here, we'll do that in three commands:

``` bash
maas admin partition mount $SYSTEM_ID $DISK1_ID $BOOT_PARTITION_ID "mount_point=/boot"
maas admin partition mount $SYSTEM_ID $DISK1_ID $ROOT_PARTITION_ID "mount_point=/"
maas admin partition mount $SYSTEM_ID $DISK2_ID $HOME_PARTITION_ID "mount_point=/home"
```

Finally, we deploy the machine. MAAS will use the partitions as we have defined them, similar to a normal Ubuntu desktop install:

``` bash
maas admin machine deploy $SYSTEM_ID
```

<h4 id="heading--interfaces">Interfaces</h4>

Using the `interfaces` constraint, you can compose virtual machines with interfaces, allowing the selection of VM host NICs.

If you don't specify an `interfaces` constraint, MAAS maintains backward compatibility by checking for a `maas` network, then a `default` network to which to connect the virtual machine.

If you specify an `interfaces` constraint, MAAS creates a `bridge` or `macvlan` attachment to the networks that match the given constraint. MAAS prefers `bridge` interface attachments when possible since this typically results in successful communication.

Consider the following interfaces constraint:

``` no-highlight
"interfaces=eth0:space=maas;eth1:space=storage"
```

Assuming you deploy the VM host on a machine or controller with access to the `maas` and `storage` [spaces](/t/concepts-and-terms/785#heading--spaces), MAAS will create an `eth0` interface bound to the `maas` space and an `eth1` interface bound to the `storage` space.

Another example tells MAAS to assign unallocated IP addresses:

``` no-highlight
interfaces=eth0:ip=172.16.99.42
```

MAAS automatically converts the `ip` constraint to a VLAN constraint (for the VLAN where its subnet can be found -- e.g. `172.16.99.0/24`.) and assigns the IP address to the newly-composed machine upon allocation.

See the [MAAS API documentation](https://maas.io/docs/api#post-maasapi20machines-opallocate) for a list of all constraint keys.

<h2 id="heading--compose-and-allocate-a-vm">Compose and allocate a VM</h2>

In the absence of any nodes in the 'New' or 'Ready' state, if a VM host of sufficient resources is available, MAAS can automatically compose (add), commission, and acquire a VM. You can accomplish this with the `allocate` sub-command:

``` bash
maas $PROFILE machines allocate
```

Note that all VM host [resource parameters](#heading--set-resources) are available to the `allocate` command, so based on the example above, the following works:

``` bash
maas $PROFILE machines allocate "storage=mylabel1:32(pool1),mylabel2:64(pool2)"
```

Once commissioned and acquired, the new machine will be ready to deploy.

[note]
The labels (i.e. `mylabel1`, `mylabel2`) in this case can be used to associate device IDs in the information MAAS dumps about the newly created VM. Try piping the output to: `jq '.constraints_by_type'`.
[/note]

<h2 id="heading--list-machine-parameters">List machine parameters</h2>

MAAS VM parameters, including their resources, are listed just like any other machine:

``` bash
maas $PROFILE machine read $SYSTEM_ID
```

<h2 id="heading--libvirt-storage-pools">Libvirt storage pools</h2>

<h3 id="heading--composing-vms-with-storage-pool-constraints">Composing VMs with storage pool constraints</h3>

See [Compose virtual machines](#heading--compose-virtual-machines).

<h3 id="heading--usage">Usage</h3>

Retrieve VM host storage pool information with the following command:

``` bash
maas $PROFILE vm-host read $VM_HOST_ID
```

Example:

``` no-highlight
Success.
Machine-readable output follows:
{
    "used": {
        "cores": 50,
        "memory": 31744,
        "local_storage": 63110426112
    },
    "name": "more-toad",
    "id": 5,
    "available": {
        "cores": 5,
        "memory": 4096,
        "local_storage": 153199988295
    },
    "architectures": [],
    "cpu_over_commit_ratio": 1.0,
    "storage_pools": [
        {
            "id": "pool_id-zvPk9C",
            "name": "name-m0M4ZR",
            "type": "lvm",
            "path": "/var/lib/name-m0M4ZR",
            "total": 47222731890,
            "used": 17226931712,
            "available": 29995800178,
            "default": true
        },
        {
            "id": "pool_id-qF87Ps",
            "name": "name-ZMaIta",
            "type": "lvm",
            "path": "/var/lib/name-ZMaIta",
            "total": 98566956569,
            "used": 15466229760,
            "available": 83100726809,
            "default": false
        },
        {
            "id": "pool_id-a6lyw5",
            "name": "name-RmDPfs",
            "type": "lvm",
            "path": "/var/lib/name-RmDPfs",
            "total": 70520725948,
            "used": 30417264640,
            "available": 40103461308,
            "default": false
        }
    ],
    "total": {
        "cores": 55,
        "memory": 35840,
        "local_storage": 216310414407
    },
    "tags": [],
    "type": "virsh",
    "memory_over_commit_ratio": 1.0,
    "pool": {
        "name": "default",
        "description": "Default pool",
        "id": 0,
        "resource_uri": "/MAAS/api/2.0/resourcepool/0/"
    },
    "zone": {
        "name": "default",
        "description": "",
        "id": 1,
        "resource_uri": "/MAAS/api/2.0/zones/default/"
    },
    "capabilities": [
        "dynamic_local_storage",
        "composable"
    ],
    "host": {
        "system_id": null,
        "__incomplete__": true
    },
    "default_macvlan_mode": null,
    "resource_uri": "/MAAS/api/2.0/pods/5/"
}
```

<h2 id="heading--delete-a-vm">Delete a VM</h2>

``` bash
maas $PROFILE machine delete $SYSTEM_ID
```

After you delete a machine, the machine's resources will be available for other VMs.

<h2 id="heading--delete-a-vm-host">Delete a VM host</h2>

``` bash
maas $PROFILE vm-host delete $VM_HOST_ID
```

[note type="caution"]
Deleting a VM host will automatically delete all machines belonging to that VM host.
[/note]

<!-- LINKS -->