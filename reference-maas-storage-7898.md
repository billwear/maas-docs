This page describes standard and custom storage layouts available in MAAS.

## Standard storage layouts

The layout descriptions below will include the EFI partition. If your system is not using UEFI, regard `sda2` as `sda1` (with an additional 512 MB available to it).

### Flat layout

With the Flat layout, a partition spans the entire boot disk. The partition is formatted with the ext4 filesystem and uses the `/` mount point:

| Name | Size        | Type | Filesystem | Mount point |
|:----:|------------:|:----:|:----------:|:------------|
| sda  | -           | disk |            |             |
| sda1 | 512 MB      | part | FAT32      | /boot/efi   |
| sda2 | rest of sda | part | ext4       | /           |

The following three options are supported:

1. `boot_size`: Size of the boot partition on the boot disk. Default is 0, meaning not to create the boot partition. The '/boot' will be placed on the root filesystem.

2. `root_device`: The block device on which to place the root partition. The default is the boot disk.

3. `root_size`: Size of the root partition. Default is 100%, meaning the entire size of the root device.

### LVM layout

The LVM layout creates the volume group `vgroot` on a partition that spans the entire boot disk. A logical volume `lvroot` is created for the full size of the volume group; is formatted with the ext4 filesystem; and uses the `/` mount point:

| Name   | Size        | Type | Filesystem     | Mount point |
|:----:|------------:|:----:|:----------:|:------------|
| sda    | -           | disk |                |             |
| sda1   | 512 MB      | part | FAT32          | /boot/efi   |
| sda2   | rest of sda | part | lvm-pv(vgroot) |             |
| lvroot | rest of sda | lvm  | ext4           | /           |
| vgroot | rest of sda | lvm  |                |             |

The following six options are supported:

1. `boot_size`: Size of the boot partition on the boot disk. Default is 0, meaning not to create the boot partition. The '/boot' will be placed on the root filesystem.
2. `root_device`: The block device on which to place the root partition. The default is the boot disk.
3. `root_size`: Size of the root partition. Default is 100%, meaning the entire size of the root device.
4. `vg_name`: Name of the created volume group. Default is `vgroot`.
5. `lv_name`: Name of the created logical volume. Default is `lvroot`.
6. `lv_size`: Size of the created logical volume. Default is 100%, meaning the entire size of the volume group.

### bcache layout

A bcache layout will create a partition that spans the entire boot disk as the backing device. It uses the smallest block device tagged with 'ssd' as the cache device. The bcache device is formatted with the ext4 filesystem and uses the `/` mount point. If there are no 'ssd' tagged block devices on the machine, then the bcache device will not be created, and the Flat layout will be used instead:

| Name      | Size        | Type | Filesystem | Mount point |
|:----:|------------:|:----:|:----------:|:------------|
| sda       | -           | disk |            |             |
| sda1      | 512 MB      | part | FAT32      | /boot/efi   |
| sda2      | rest of sda | part | bc-backing |             |
| sdb (ssd) | -           | disk |            |             |
| sdb1      | 100% of sdb | part | bc-cache   |             |
| bcache0   | per sda2    | disk | ext4       | /           |

The following seven options are supported:

1. `boot_size`: Size of the boot partition on the boot disk. Default is 0, meaning not to create the boot partition. The '/boot' will be placed on the root filesystem.
2. `root_device`: The block device upon which to place the root partition. The default is the boot disk.
3. `root_size`: Size of the root partition. Default is 100%, meaning the entire size of the root device.
4. `cache_device`: The block device to use as the cache device. Default is the smallest block device tagged ssd.
5. `cache_mode`: The cache mode to which MAAS should set the created bcache device. The default is `writethrough`.
6. `cache_size`: The size of the partition on the cache device. Default is 100%, meaning the entire size of the cache device.
7. `cache_no_part`: Whether or not to create a partition on the cache device. Default is false, meaning to create a partition using the given `cache_size`. If set to true, no partition will be created, and the raw cache device will be used as the cache.

vmfs6 storage layout reference
### VMFS6 layout

The VMFS6 layout is used for VMware ESXi deployments only. It is required when configuring VMware VMFS Datastores. This layout creates all operating system partitions, in addition to the default datastore. The datastore may be modified. New datastores may be created or extended to include other storage devices. The base operating system partitions may not be modified because VMware ESXi requires them. Once applied another storage layout must be applied to remove the operating system partitions.

| Name | Size      | Type    | Use               |
|:-----|------------:|:----:|:----------|
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

- `root_device`: The block device upon which to place the root partition. Default is the boot disk.

- `root_size`: Size of the default VMFS Datastore. Default is 100%, meaning the remaining size of the root disk.

### Blank layout

The blank layout removes all storage configuration from all storage devices. It is useful when needing to apply a custom storage configuration.


Machines with the blank layout applied are not deployable; you must first configure storage manually.

## Custom storage layouts

This section provides examples of commonly-used custom storage layouts for MAAS-deployed machines.  These examples provide custom storage layout configurations that a script could output to the `$MAAS_STORAGE_CONFIG_FILE`. For clarity, this section assumes that the machine has 5 disks (named `sda` to `sde`); be sure to adjust accordingly.

There is no need to add entries for those devices in the `layout` section if the disks are not explicitly partitioned, but just used by other devices (e.g. RAID or LVM).

### GPT partitioning
Here is an example of a simple single-disk layout with GPT partitioning.

```nohighlight
{
  "layout": {
    "sda": {
      "type": "disk",
      "ptable": "gpt",
      "boot": true,
      "partitions": [
        {
          "name": "sda1",
          "fs": "vfat",
          "size": "500M",
          "bootable": true
        },
        {
          "name": "sda2",
          "size": "5G",
          "fs": "ext4"
        },
        {
          "name": "sda3",
          "size": "2G",
          "fs": "swap"
        },
        {
          "name": "sda4",
          "size": "120G",
          "fS": "ext4"
        }
      ]
    }
  },
  "mounts": {
    "/": {
      "device": "sda2",
      "options": "noatime"
    },
    "/boot/efi": {
      "device": "sda1"
    },
    "/data": {
      "device": "sda4"
    },
    "none": {
      "device": "sda3"
    }
  }
}
```
In the `mounts` section, options for mount points can be specified. For swap, an entry must be present (with any unique name that doesn't start with a `/`), otherwise the swap will be created but not activated.

### RAID 5 
This example describes the reference setup for a RAID 5 configuration with spare devices.

```nohighlight
{
  "layout": {
    "storage": {
      "type": "raid",
      "level": 5,
      "members": [
        "sda",
        "sdb",
        "sdc"
      ],
      "spares": [
        "sdd",
        "sde"
      ],
      "fs": "btrfs"
    }
  },
  "mounts": {
    "/data": {
      "device": "storage"
    }
  }
}
```
Both full disks and partitions can be used as RAID members.

### Pre-defined LVM
In this reference example, you can find an LVM with pre-defined volumes.

```nohighlight
{
  "layout": {
    "storage": {
      "type": "lvm",
      "members": [
        "sda",
        "sdb",
        "sdc",
        "sdd"
      ],
      "volumes": [
        {
          "name": "data1",
          "size": "1T",
          "fs": "ext4"
        },
        {
          "name": "data2",
          "size": "2.5T",
          "fs": "btrfs"
        }
      ]
    }
  },
  "mounts": {
    "/data1": {
      "device": "data1"
    },
    "/data2": {
      "device": "data2"
    }
  }
}
```
If no volumes are specified, the volume group is still created.

### Bcache
Here is an example of a custom `bcache` setup.

```nohighlight
{
  "layout": {
     "data1": {
      "type": "bcache",
      "cache-device": "sda",
      "backing-device": "sdb",
      "cache-mode": "writeback",
      "fs": "ext4"
    },
    "data2": {
      "type": "bcache",
      "cache-device": "sda",
      "backing-device": "sdc",
      "fs": "btrfs"
    }
  },
  "mounts": {
    "/data1": {
      "device": "data1"
    },
    "/data2": {
      "device": "data2"
    }
  }
}
```
The same cache set can be used by different bcaches by specifying the same `backing-device` for them.

### LVM RAID with bcache
This more complex reference example describes an LVM on top of RAID with bcache enabled for read-intensive operations.

```nohighlight
{
  "layout": {
    "bcache0": {
      "type": "bcache",
      "backing-device": "sda",
      "cache-device": "sdf"
    },
    "bcache1": {
      "type": "bcache",
      "backing-device": "sdb",
      "cache-device": "sdf"
    },
    "bcache2": {
      "type": "bcache",
      "backing-device": "sdc",
      "cache-device": "sdf"
    },
    "bcache3": {
      "type": "bcache",
      "backing-device": "sdd",
      "cache-device": "sdf"
    },
    "bcache4": {
      "type": "bcache",
      "backing-device": "sde",
      "cache-device": "sdf"
    },
    "raid": {
      "type": "raid",
      "level": 5,
      "members": [
        "bcache0",
        "bcache1",
        "bcache2"
      ],
      "spares": [
        "bcache3",
        "bcache4"
      ]
    },
    "lvm": {
      "type": "lvm",
      "members": [
        "raid"
      ],
      "volumes": [
        {
          "name": "root",
          "size": "10G",
          "fs": "ext4"
        },
        {
          "name": "data",
          "size": "3T",
          "fs": "btrfs"
        }
      ]
    }
  },
  "mounts": {
   "/": {
      "device": "root"
    },
    "/data": {
      "device": "data"
    }
  }
}
```
The RAID is created by using 5 bcache devices, each one using a different disk and the same SSD cache device. LVM is created on top of the RAID device and volumes are then created in it, to provide partitions.
