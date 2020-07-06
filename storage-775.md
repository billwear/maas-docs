You have significant latitude when choosing the final storage configuration of a deployed machine. MAAS supports traditional disk partitioning, as well as more complex options such as LVM, RAID, and bcache. MAAS also supports UEFI as a boot mechanism.  This article explains boot mechanisms and layouts, and offers some advice on how to configure layouts and manage storage.

#### Quick questions you may have:

* [How does UEFI booting work?](/t/storage/775#heading--uefi)
* [What kind of storage layouts are available?](/t/storage/775#heading--layouts)
* [How do I set storage layouts?](/t/storage/775#heading--setting-layouts)
* [What storage modifications can I make at the filesystem level?](/t/storage/775#heading--final-storage-modifications)
* [How can I erase a disk?](/t/disk-erasure/774)

A machine's storage is dependant upon the underlying system's disks, but its configuration (i.e., disk usage) is the result of a storage template. In MAAS, this template is called a layout, and MAAS applies it to a machine during commissioning.  Once a layout is applied, a regular user can make modifications to a machine at the filesystem level to arrive at the machine's final storage configuration.  When a machine is no longer needed, a user can choose from among several disk erasure types before releasing it.

[note]
MAAS supports storage configuration for CentOS and RHEL deployments. Support includes RAID, LVM, and custom partitioning with different file systems (ZFS and bcache excluded). This support requires a newer version of Curtin, [available as a PPA](https://launchpad.net/ubuntu/+source/curtin).
[/note]

<h2 id="heading--uefi">UEFI</h2>

Every layout type supports a machine booting with UEFI. In such a case, MAAS automatically creates an EFI boot partition (`/boot/efi`). Other than setting the machine to boot from UEFI, the user does not need to take any additional action.

[note type="negative" status="Warning"]
UEFI must be enabled or disabled for the lifespan of the machine. For example, do not enlist a machine with UEFI enabled, and then disable it before commissioning. It won't work!
[/note]

The EFI partition, if created, will be the first partition (`sda1`) and will have a FAT32 filesystem with a size of 512 MB.

<h2 id="heading--layouts">Layouts</h2>

There are three layout types:

-   Flat layout
-   LVM layout
-   bcache layout

The layout descriptions below will include the EFI partition. If your system is not using UEFI, regard `sda2` as `sda1` (with an additional 512 MB available to it).

<h3 id="heading--flat-layout">Flat layout</h3>

With the Flat layout, a partition spans the entire boot disk. The partition is formatted with the ext4 filesystem and uses the `/` mount point:

| Name | Size        | Type | Filesystem | Mount point |
|:-----|-------------|------|------------|-------------|
| sda  | -           | disk |            |             |
| sda1 | 512 MB      | part | FAT32      | /boot/efi   |
| sda2 | rest of sda | part | ext4       | /           |

The following options are supported:

`boot_size`: Size of the boot partition on the boot disk. Default is 0, meaning not to create the boot partition. The '/boot' will be placed on the root filesystem.

`root_device`: The block device on which to place the root partition. The default is the boot disk.

`root_size`: Size of the root partition. Default is 100%, meaning the entire size of the root device.

<h3 id="heading--lvm-layout">LVM layout</h3>

The LVM layout creates the volume group `vgroot` on a partition that spans the entire boot disk. A logical volume `lvroot` is created for the full size of the volume group; is formatted with the ext4 filesystem; and uses the `/` mount point:

| Name   | Size        | Type | Filesystem     | Mount point |
|:-------|-------------|------|----------------|-------------|
| sda    | -           | disk |                |             |
| sda1   | 512 MB      | part | FAT32          | /boot/efi   |
| sda2   | rest of sda | part | lvm-pv(vgroot) |             |
| lvroot | rest of sda | lvm  | ext4           | /           |
| vgroot | rest of sda | lvm  |                |             |

The following options are supported:

`boot_size`: Size of the boot partition on the boot disk. Default is 0, meaning not to create the boot partition. The '/boot' will be placed on the root filesystem.

`root_device`: The block device on which to place the root partition. The default is the boot disk.

`root_size`: Size of the root partition. Default is 100%, meaning the entire size of the root device.

`vg_name`: Name of the created volume group. Default is `vgroot`.

`lv_name`: Name of the created logical volume. Default is `lvroot`.

`lv_size`: Size of the created logical volume. Default is 100%, meaning the entire size of the volume group.

<h3 id="heading--bcache-layout">bcache layout</h3>

A bcache layout will create a partition that spans the entire boot disk as the backing device. It uses the smallest block device tagged with 'ssd' as the cache device. The bcache device is formatted with the ext4 filesystem and uses the `/` mount point. If there are no 'ssd' tagged block devices on the machine, then the bcache device will not be created, and the Flat layout will be used instead:

| Name      | Size        | Type | Filesystem | Mount point |
|:----------|-------------|------|------------|-------------|
| sda       | -           | disk |            |             |
| sda1      | 512 MB      | part | FAT32      | /boot/efi   |
| sda2      | rest of sda | part | bc-backing |             |
| sdb (ssd) | -           | disk |            |             |
| sdb1      | 100% of sdb | part | bc-cache   |             |
| bcache0   | per sda2    | disk | ext4       | /           |

The following options are supported:

`boot_size`: Size of the boot partition on the boot disk. Default is 0, meaning not to create the boot partition. The '/boot' will be placed on the root filesystem.

`root_device`: The block device upon which to place the root partition. The default is the boot disk.

`root_size`: Size of the root partition. Default is 100%, meaning the entire size of the root device.

`cache_device`: The block device to use as the cache device. Default is the smallest block device tagged ssd.

`cache_mode`: The cache mode to which MAAS should set the created bcache device. The default is `writethrough`.

`cache_size`: The size of the partition on the cache device. Default is 100%, meaning the entire size of the cache device.

`cache_no_part`: Whether or not to create a partition on the cache device. Default is false, meaning to create a partition using the given `cache_size`. If set to true, no partition will be created, and the raw cache device will be used as the cache.

<h3 id="heading--vmfs6-layout">VMFS6 layout</h3>

The VMFS6 layout is used for VMware ESXi deployments only. It is required when configuring VMware VMFS Datastores. This layout creates all operating system partitions, in addition to the default datastore. The datastore may be modified.  New datastores may be created or extended to include other storage devices. The base operating system partitions may not be modified because VMware ESXi requires them. Once applied another storage layout must be applied to remove the operating system partitions.

| Name | Size      | Type    | Use               |
|:-----|-----------|---------|-------------------|
| sda  | -         | disk    |                   |
| sda1 | 3 MB      | part    | EFI               |
| sda2 | 4 GB      | part    | Basic Data        |
| sda3 | Remaining | part    | VMFS Datastore 1  |
| sda4 | -         | skipped |                   |
| sda5 | 249 MB    | part    | Basic Data        |
| sda6 | 249 MB    | part    | Basic Data        |
| sda7 | 109 MB    | part    | VMware Diagnostic |
| sda8 | 285 MB    | part    | Basic Data        |
| sda9 | 2.5 GB    | part    | VMware Diagnostic |

The following options are supported:

`root_device`: The block device upon which to place the root partition. Default is the boot disk.

`root_size`: Size of the default VMFS Datastore. Default is 100%, meaning the remaining size of the root disk.

<h3 id="heading--blank-layout">Blank layout</h3>

The blank layout removes all storage configuration from all storage devices. It is useful when needing to apply a custom storage configuration.

[note type="negative" status="Warning"]
Machines with the blank layout applied are not deployable; you must first configure storage manually.
[/note]

<h2 id="heading--setting-layouts">Setting layouts</h2>

Layouts can be set globally and on a per-machine basis.

<h3 id="heading--default-layout">Default layout</h3>

All machines will have a default layout applied when commissioned. An administrator can configure the default layout on the 'Settings' page, under the 'Storage' tab.

![default storage layout](https://discourse.maas.io/uploads/default/original/1X/80de3bc701552cd00bec707830accf380c214b17.png)

See [Disk erasure](/t/disk-erasure/774) for an explanation of the options related to the erasing of disks.

[note type="caution" status="Important"]
The new default will only apply to newly-commissioned machines.
[/note]

To change the default with the CLI see [MAAS CLI - advanced tasks](/t/advanced-cli-tasks/793#heading--set-the-default-storage-layout).

<h3 id="heading--node-layout">Machine layout</h3>

An administrator can change the layout for a single machine as well as customise that layout providing this is done while the machine has a status of 'Ready'. This is only possible via the CLI at this time (see [MAAS CLI - advanced tasks](/t/advanced-cli-tasks/793#heading--set-a-storage-layout)).

[note]
Only an administrator can modify storage at the block device level (providing the machine has a status of 'Ready').
[/note]

<h2 id="heading--final-storage-modifications">Final storage modifications</h2>

Once MAAS provisions a machine with block devices, via a layout or administrator customisation, a regular user can modify the resulting storage configuration at the filesystem level.

<h2 id="heading--disk-erasure">Disk erasure</h2>

You can erase machine storage in several ways. See [Disk erasure](/t/disk-erasure/774) for details.

<!-- LINKS -->