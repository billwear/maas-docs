"Storage pools” are storage resources managed by a VM host. A storage pool is a given amount of storage set aside for use by VMs. A pool can be organized into storage volumes, assigned to VMs as individual block devices. More information is available for both [libvirt](https://libvirt.org/storage.html) and [LXD](https://lxd.readthedocs.io/en/latest/storage/) storage pools.

[note]
For the Beta version of LXD VM hosts, each VM can be assigned a single block device from the storage pool.
[/note]

MAAS displays information about each VM host's storage pools so you can understand your resource usage at a glance:

![storage-pools|690x183](https://discourse.maas.io/uploads/default/optimized/1X/3387f256f9bd02f7fc2079f119377305256973c8_2_690x183.jpeg)