MAAS can configure custom local VMware VMFS Datastore layouts to maximise the usage of your local disks when deploying VMware ESXi. As VMware ESXi requires specific partitions for operating system usage, you must first apply the [VMFS6 storage layout](/t/storage/775#VMFS6%20layout). This layout creates a VMFS Datastore named datastore1 which uses the disk space left over on the boot disk after MAAS creates the operating system partitions.

#### Quick questions you may have:

* [How do I list the VMFS datastores on a machine?](/t/vmware-vmfs-datastores/780#heading--listing-vmfs-datastores)
* [How do I view a specific datastore on a machine?](/t/vmware-vmfs-datastores/780#heading--viewing-vmfs-datastores)
* [How do I create a new datastore?](/t/vmware-vmfs-datastores/780#heading--creating-vmfs-datastores)
* [How do I edit a datastore?](/t/vmware-vmfs-datastores/780#heading--editing-vmfs-datastores)
* [How do I delete a datastore?](/t/vmware-vmfs-datastores/780#heading--deleting-vmfs-datastores)

<h2 id="heading--listing-vmfs-datastores">Listing VMFS datastores</h2>

To view all VMFS Datastores on a machine, use the 'vmfs-datastores read' API call:

``` bash
maas $PROFILE vmfs-datastores read $SYSTEM_ID
```

``` nohighlight
[
    {
        "human_size": "45.8 GB",
        "filesystem": {
            "fstype": "vmfs6",
            "mount_point": "/vmfs/volumes/datastore1"
        },
        "uuid": "2779a745-1db3-4fd7-b06e-455b728fffd4",
        "name": "datastore1",
        "system_id": "4qxaga",
        "devices": [
            {
                "uuid": "c55fe657-689d-4570-8492-683dd5fa1c40",
                "size": 35026632704,
                "bootable": false,
                "tags": [],
                "used_for": "VMFS extent for datastore1",
                "filesystem": {
                    "fstype": "vmfs6",
                    "label": null,
                    "uuid": "55ac6422-68b5-440e-ba65-153032605b51",
                    "mount_point": null,
                    "mount_options": null
                },
                "type": "partition",
                "device_id": 5,
                "path": "/dev/disk/by-dname/sda-part3",
                "system_id": "4qxaga",
                "id": 71,
                "resource_uri": "/MAAS/api/2.0/nodes/4qxaga/blockdevices/5/partition/71"
            },
            {
                "uuid": "5182e503-4ad4-446e-9660-fd5052b41cc5",
                "size": 10729029632,
                "bootable": false,
                "tags": [],
                "used_for": "VMFS extent for datastore1",
                "filesystem": {
                    "fstype": "vmfs6",
                    "label": null,
                    "uuid": "a5949b18-d591-4627-be94-346d0fdaf816",
                    "mount_point": null,
                    "mount_options": null
                },
                "type": "partition",
                "device_id": 6,
                "path": "/dev/disk/by-dname/sdb-part1",
                "system_id": "4qxaga",
                "id": 77,
                "resource_uri": "/MAAS/api/2.0/nodes/4qxaga/blockdevices/6/partition/77"
            }
        ],
        "id": 17,
        "size": 45755662336,
        "resource_uri": "/MAAS/api/2.0/nodes/4qxaga/vmfs-datastore/17/"
    }
]
```

<h2 id="heading--viewing-vmfs-datastores">Viewing a specific datastore</h2>

To view a specific VMFS Datastores on a machine, use the 'vmfs-datastore read' API call:

``` bash
maas $PROFILE vmfs-datastore read $SYSTEM_ID $VMFS_DATASTORE_ID
```

``` nohighlight
{
    "uuid": "fb6fedc2-f711-40de-ab83-77eddc3e19ac",
    "name": "datastore1",
    "system_id": "b66fn6",
    "id": 18,
    "filesystem": {
        "fstype": "vmfs6",
        "mount_point": "/vmfs/volumes/datastore1"
    },
    "human_size": "2.8 GB",
    "devices": [
        {
            "uuid": "b91df576-ba02-4acb-914f-03ba9a2865b7",
            "size": 2814377984,
            "bootable": false,
            "tags": [],
            "system_id": "b66fn6",
            "used_for": "VMFS extent for datastore1",
            "type": "partition",
            "id": 80,
            "filesystem": {
                "fstype": "vmfs6",
                "label": null,
                "uuid": "4a098d71-1e59-4b5f-932d-fc30a1c0dc96",
                "mount_point": null,
                "mount_options": null
            },
            "device_id": 1,
            "path": "/dev/disk/by-dname/vda-part3",
            "resource_uri": "/MAAS/api/2.0/nodes/b66fn6/blockdevices/1/partition/80"
        }
    ],
    "size": 2814377984,
    "resource_uri": "/MAAS/api/2.0/nodes/b66fn6/vmfs-datastore/18/"
}
```

<h2 id="heading--creating-vmfs-datastores">Creating a new VMFS Datastore</h2>

A VMware VMFS datastore is created on one or more [block devices](/t/block-devices/749) or [partitions](/t/block-devices/749)

To create a VMFS Datastores on a machine use the 'vmfs-datastores create' API call:

``` bash
maas $PROFILE vmfs-datastores create $SYSTEM_ID name=$VMFS_NAME block_devices=$BLOCK_ID_1,$BLOCK_ID_2 partitions=$PARTITION_ID_1,$PARTITION_ID_2
```

``` nohighlight
{
    "system_id": "b66fn6",
    "devices": [
        {
            "uuid": "b91df576-ba02-4acb-914f-03ba9a2865b7",
            "size": 2814377984,
            "bootable": false,
            "tags": [],
            "device_id": 1,
            "system_id": "b66fn6",
            "type": "partition",
            "used_for": "VMFS extent for datastore42",
            "filesystem": {
                "fstype": "vmfs6",
                "label": null,
                "uuid": "fc374367-a2fb-4e50-9377-768bfe9705b6",
                "mount_point": null,
                "mount_options": null
            },
            "path": "/dev/disk/by-dname/vda-part3",
            "id": 80,
            "resource_uri": "/MAAS/api/2.0/nodes/b66fn6/blockdevices/1/partition/80"
        }
    ],
    "name": "datastore42",
    "filesystem": {
        "fstype": "vmfs6",
        "mount_point": "/vmfs/volumes/datastore42"
    },
    "id": 19,
    "size": 2814377984,
    "uuid": "2711566c-2df4-4cc4-8c06-7392bb1f9532",
    "human_size": "2.8 GB",
    "resource_uri": "/MAAS/api/2.0/nodes/b66fn6/vmfs-datastore/19/"
}
```

<h2 id="heading--editing-vmfs-datastores">Edit an existing VMFS Datastore</h2>

To edit an existing VMFS Datastores on a machine use the 'vmfs-datastore update' API call:

``` bash
maas $PROFILE vmfs-datastore update $SYSTEM_ID $VMFS_ID name=$NEW_VMFS_NAME add_block_devices=$NEW_BLOCK_ID_1,$NEW_BLOCK_ID_2 add_partitions=$NEW_PARTITION_ID_1,$NEW_PARTITION_ID_2 remove_partitions=$EXISTING_PARTITION_ID1,$EXISTING_PARTITION_ID2
```

``` nohighlight
{
    "uuid": "2711566c-2df4-4cc4-8c06-7392bb1f9532",
    "name": "datastore42",
    "system_id": "b66fn6",
    "id": 19,
    "filesystem": {
        "fstype": "vmfs6",
        "mount_point": "/vmfs/volumes/datastore42"
    },
    "human_size": "13.5 GB",
    "devices": [
        {
            "uuid": "b91df576-ba02-4acb-914f-03ba9a2865b7",
            "size": 2814377984,
            "bootable": false,
            "tags": [],
            "system_id": "b66fn6",
            "used_for": "VMFS extent for datastore42",
            "type": "partition",
            "id": 80,
            "filesystem": {
                "fstype": "vmfs6",
                "label": null,
                "uuid": "fc374367-a2fb-4e50-9377-768bfe9705b6",
                "mount_point": null,
                "mount_options": null
            },
            "device_id": 1,
            "path": "/dev/disk/by-dname/vda-part3",
            "resource_uri": "/MAAS/api/2.0/nodes/b66fn6/blockdevices/1/partition/80"
        },
        {
            "uuid": "f21fe54e-b5b1-4562-ab6b-e699e99f002f",
            "size": 10729029632,
            "bootable": false,
            "tags": [],
            "system_id": "b66fn6",
            "used_for": "VMFS extent for datastore42",
            "type": "partition",
            "id": 86,
            "filesystem": {
                "fstype": "vmfs6",
                "label": null,
                "uuid": "f3d9b6a3-bab3-4677-becb-bf5a421bfcc2",
                "mount_point": null,
                "mount_options": null
            },
            "device_id": 2,
            "path": "/dev/disk/by-dname/vdb-part1",
            "resource_uri": "/MAAS/api/2.0/nodes/b66fn6/blockdevices/2/partition/86"
        }
    ],
    "size": 13543407616,
    "resource_uri": "/MAAS/api/2.0/nodes/b66fn6/vmfs-datastore/19/"
}
```

<h2 id="heading--deleting-vmfs-datastores">Deleting a VMFS Datastore</h2>

To delete a VMFS Datastores on a machine use the 'vmfs-datastore delete' API call:

``` bash
maas $PROFILE vmfs-datastore delete $SYSTEM_ID $VMFS_ID
```