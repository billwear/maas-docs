[note type="warning" status="Deprecated page"]
This page has been deprecated in favor of newer terminology and technology.  Please see the [updated page](/t/introduction-to-vm-hosting/1524).
[/note]

KVM in MAAS allows for the dynamic composition of nodes from a pool of available hardware resources (e.g. disk space, memory, cores).

MAAS currently supports KVM via Virsh.

KVM is useful for Juju integration, allowing for dynamic allocation of VMs with custom interface constraints. Alternatively, if you would like to use MAAS to manage a collection of VMs, the robust web UI allows you to easily create and manage VMs, logically grouped by KVM host.

Features:

-   Juju integration
-   At-a-glance visual tools for easy resource management
-   Set overcommit ratios for physical resources such as CPU and RAM
-   Assign KVMs to resource pools to segregate your KVMs into logical groupings
-   Track libvirt storage pool usage and assign default storage pools to your KVM hosts
-   Create VMs on multiple networks, specified by space, subnet, VLAN, or IP address

<h2 id="heading--next-steps">Next steps</h2>

-   [Add a KVM host](/t/add-a-kvm-h-ost/805)

<!-- LINKS -->