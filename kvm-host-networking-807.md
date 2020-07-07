[note type="warning" status="Deprecated page"]
This page has been deprecated in favor of newer terminology and technology.  Please see the [updated page](/t/vm-host-networking/1526).
[/note]

To enable KVM host networking features, MAAS must match the KVM host IP address of a potential KVM host with a known device (a machine or controller). For example, if a machine not known to MAAS is set up as a KVM host, enhanced interface selection features will not be available.

We recommend that you deploy a machine within MAAS, and then check the "Install MAAS-managed KVM Host" checkbox (a full explanation is found [here](/t/add-a-kvm-h-ost/805)). MAAS will automatically install KVM as well as ensure that the network model is consistent with what is on the machine.

There are other ways of setting up KVM hosts that provide easy management of VMs via the MAAS UI. You can, for example, install KVM manually on a deployed node or a new or existing rack controller.

[note type="caution"]
Enhanced KVM host networking features may not operate correctly when you install KVM manually on a deployed node. (E.g. if any of the host interfaces change.)
[/note]

#### Quick questions you may have:

* [What should I know about KVM host networking with MAAS 2.4 and earlier?](/t/kvm-host-networking/807#heading--24-and-earlier)
* [What should I know about KVM host networking with MAAS 2.5 and later?](/t/kvm-host-networking/807#heading--25)
* [What should I know about bridges and KVM host networking?](/t/kvm-host-networking/807#heading--bridges)

<h2 id="heading--24-and-earlier">2.4 and earlier</h2>

MAAS requires the use of a DHCP server it can control. Therefore, DHCP must be enabled in MAAS (rather than in libvirt) to allow VMs to use network booting (e.g. PXE). A DHCP relay can also forward DHCP requests to MAAS.

MAAS first checks for the existence of a libvirt network named `maas`. The `maas` network should have DHCP disabled in favour of MAAS-enabled DHCP to allow your VMs to network boot. VMs on the `maas` libvirt network must be able to reach the wider network. As such, either (1) the bridge the VMs are attached to must include one of the host's physical network interfaces on the appropriate network, or (2) NAT must be enabled.

If MAAS cannot find a `maas` network, it will fallback to libvirt's `default` network.

[note]
Libvirt's `default` network has DHCP enabled by default. You must either disable libvirt's DHCP and enable MAAS DHCP on the `default` network in libvirt, or create a separate `maas` network on a VLAN with MAAS DHCP enabled.
[/note]

<h2 id="heading--25">2.5+</h2>

In MAAS 2.5, you can apply interface-constraints to VMs, which allows you to compose a VM with specific networking requirements. Y(a full-explanation of the feature is found in the [following section](/t/creating-and-deleting-new-vms/806#heading--interfaces)). When you meet these requirements, MAAS can connect your VMs to the full range of MAAS-configured network.

<h3 id="heading--with-interface-constraints">With interface constraints</h3>

Instead of attaching to a libvirt network like `maas` or `default`, MAAS in this case tells the hypervisor on the host to attach the VM directly to a constraints-matching underlying bridge or non-bridge interface via macvtap. If you provide a specific IP address in the constraint string, MAAS will try to allocate it and assign it to the interface when creating the VM. This feature offers some limited interface configuration capability during the creation process.

<h3 id="heading--with-no-interface-constraints">With no interface constraints</h3>

If you do not specify interface constraints, MAAS attaches the VM to a network based on KVM installation mode.

If the [MAAS deployed a host](/t/add-a-kvm-h-ost/805) for use as a KVM host (recommended), MAAS will skip the libvirt `maas` and `default` networks if they are not enabled for DHCP in MAAS, instead preferring a DHCP-enabled MAAS network. This arrangement means you don't have to manually create a MAAS-friendly libvirt network to which to attach the VM as with previous versions of MAAS.

If you installed KVM manually on your host after deploying via MAAS, MAAS will revert to its 2.4 behaviour.  Namely, MAAS will try to attach to a suitable `maas` or `default` libvirt network, enabling network booting if it detects MAAS-enabled DHCP on either.

<h2 id="heading--bridges">Bridges</h2>

<h3 id="heading--macvlan">Macvlan</h3>

MAAS uses macvlan if an interfaces constraint specifies a macvlan interface when composing a VM.

You can configure the default macvlan mode of an existing KVM host using the CLI:

``` bash
maas $PROFILE pod update <pod-id> host=<host> default_macvlan_mode=<mode>
```

Where:

-   `pod-id`: [The pod’s MAAS ID](/t/creating-and-deleting-new-vms/806#heading--find-pod-ids)
-   `host`: The IP or hostname of the libvirt instance
-   `default_macvlan_mode`:
    -   `private`: VMs with the same parent interface cannot communicate but can reach destinations outside the parent interface
    -   `vepa`: VM communication is forwarded through the parent interface and reflected if the source and destination of the packet are local to the VMs. VMs can reach destinations outside the parent interface. Note that this requires a VEPA switching mechanism.
    -   `bridge`: Inter-VM communication is maintained locally (behind the parent interface), and destinations outside are forwarded through the parent interface.
    -   `passthru`: Allows a single VM to be connected directly to the parent interface, which in turn allows the VM to change MAC addresses and other interface parameters.

<h3 id="heading--bridge-vs-macvlan">Bridge vs. macvlan</h3>

Unless you have a specific reason to use macvlan, a bridge is the better choice for most situations. macvlan is simpler in design than a bridge, so it offers better throughput and less demand on CPU.  Remember, though, that a bridge is typically easier to configure -- and more likely to result in successful communication.

<!-- LINKS -->