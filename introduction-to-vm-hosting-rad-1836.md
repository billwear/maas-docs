VM hosts in MAAS allow for the dynamic composition of nodes from a pool of available hardware resources (e.g. disk space, memory, cores).  You can create virtual machines (VMs) as needed within the limits of your resources, without concern for physical hardware.  MAAS currently supports VM hosts and VMs created via [libvirt](https://ubuntu.com/server/docs/virtualization-libvirt).  As part of MAAS 2.8, we also support LXD VMs and VM hosts as a Beta feature.

VM hosts are particulary useful for Juju integration, allowing for dynamic allocation of VMs with custom interface constraints. Alternatively, if you would like to use MAAS to manage a collection of VMs, the robust web UI allows you to easily create and manage VMs, logically grouped by VM host.  Conspicuous features include:

-   Juju integration
-   At-a-glance visual tools for easy resource management
-   Set overcommit ratios for physical resources such as CPU and RAM
-   Assign VMs to resource pools to segregate your VMs into logical groupings
-   Track VM host storage pool usage and assign default storage pools
-   Create VMs on multiple networks, specified by space, subnet, VLAN, or IP address

This section will lead you through the creation, usage, and management of VM hosts.  Note that, as LXD VM hosts are still in Beta, some screen terminology has not yet been changed to reflect "VM host" instead of "KVM."  You will still use the "KVM" tab on the top of the MAAS dashboard to reach the VM host functionality, and you may see buttons which refer to "Add KVM," for example, instead of "Add VM host."  Rest assured that the Beta LXD VM host functionality is fully available through these channels.

#### Quick questions you may have:

* [What is a VM host?](/t/introduction-to-vm-hosting/1524#heading--what-is-a-vm-host)
* [Which VM host should I use: LXD or KVM?](/t/introduction-to-vm-hosting/1524#heading--choosing-vm-host-type)
* [How do I set up SSH for use by libvirt?](/t/vm-host-networking/1526#heading--set-up-ssh)
* [How do I add a VM host?](/t/adding-a-vm-host-2-8-draft/1549)

<h2 id="heading--what-is-a-vm-host">What is a VM host?</h2>

Simply put, a VM host is a machine which is designated to run virtual machines (VMs).  A VM host divides its resources (CPU cores, RAM, storage) among the number of VMs you want to create, based on choices that you make when creating each VM.  It is also possible to overcommit resources -- that is, use more resources than the VM host actually has available -- as long as you use the VMs carefullly. Once MAAS has enlisted, commissioned, and acquired a newly-added machine, you can deploy it as a VM host.

<h2 id="heading--choosing-vm-host-type">LXD VM host  (Beta) vs. libvirt</h2>

Libvirt KVMs and LXD VMs are both based on the same underlying virtualization technology, QEMU.  Unlike libvirt KVMs, though, LXD VMs can be managed without requiring SSH access to the VM host. LXD are remotely accessed via secure HTTP transport, which provides better security for LXD-based VMs.  In addition, LXD has a better API, and is part of a much larger constellation of enterprise software, offering a wider range of future features and use cases.