MAAS provides supported images for stable Ubuntu releases, and for CentOS 6.6, CentOS 7, and CentOS 8.0 releases.  [Ubuntu Advantage](https://www.ubuntu.com/support) is needed to build customer Windows, RHEL and ESXi images.

#### Quick questions you may have:

* [What are boot sources, and why do they matter?](/t/images/754#heading--boot-sources)
* [How do I select and import an image?](/t/select-and-import-images/751)
* [How do I set up and use a local image mirror?](/t/local-image-mirror/752)
* [How do I use and manage VMWare images?](/t/vmware-images/753)
* [How do I manage images with the CLI?](/t/cli-image-management/797)

MAAS stores images in the region controller's database, from where the rack controller proxies them to the individual machines.  It's important to note that for ESXi images, network configuration includes only:

-   DHCP
-   Static/auto IP assignments
-   Aliases
-   VLANs
-   Bonds -- bonds are mapped to NIC teaming as follows:
    -   balance-rr -- portid
    -   active-backup -- explicit
    -   802.3ad -- iphash, LACP rate and XMIT hash policy settings ignored

See [CLI Image Management](/t/cli-image-management/797) for information on advanced image management.

<h2 id="heading--boot-sources">Boot sources</h2>

The place from where a region controller downloads its images is known as a boot source. The main characteristics of a boot source are its location (URL) and its associated GPG public keyring.

[note]
A boot resource is another name for an image. So boot resources are found within a boot source.
[/note]

MAAS comes configured with a boot source that should suffice for most users:

[`https://images.maas.io/ephemeral-v3/daily/`](https://images.maas.io/ephemeral-v3/daily/)

The above URL points to the 'daily' stream (for the v3 format). See [Local image mirror](/t/local-image-mirror/752) for some explanation regarding the availability of other streams.

Although the backend supports multiple boot sources, MAAS itself uses a single source. If multiple sources are detected, the web UI will print a warning and will be unable to manage images.

<!-- LINKS -->