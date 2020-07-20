Following on from MAAS 2.7 ([2.7 release notes](/t/historical-release-notes/835)), we are happy to announce that MAAS 2.8 is now available. This release features some critical bug fixes, along with some exciting new features.

<h4>Quick questions you may have:</h4>

- [Where are the release notes for 2.7?](/t/historical-release-notes/835)
- [What are the new features & fixes for 2.8?](/t/whats-new-in-maas-2-8/1655#heading--2-8-release-notes)
- [What known issues should I be aware of?](/t/whats-new-in-maas-2-8/1655#heading--2-8-known-issues)
- [How do I install MAAS 2.8 as a snap?](https://discourse.maas.io/t/maas-installation-from-a-snap/773)
- [How do I upgrade my MAAS 2.7 snap to a MAAS 2.8 snap?](https://discourse.maas.io/t/maas-installation-from-a-snap/773#heading--upgrade-maas-snap)
- [What bugs were fixed in this release?](/t/whats-new-in-maas-2-8/1655#heading--bug-fixes)

<h2 id="heading--2-8-release-notes">LXD-based VM host support (Beta)</h2>

MAAS 2.8 adds the beta capability to use LXD-based VM hosts and virtual machines (VMs), in addition to the [libvirt](https://ubuntu.com/server/docs/virtualization-libvirt)-based VM hosts/VMs already available.  These new LXD VM hosts use the same underlying technology as libvirt (QEMU). Unlike libvirt KVMs, though, LXD VMs can be managed without requiring SSH access to the VM host. LXD are remotely accessed via secure HTTP transport, which provides better security for LXD-based VMs. In addition, LXD has a better API, and is part of a much larger constellation of enterprise software, offering a wider range of future features and use cases.

## UI performance improvements for the machine listing page

Within MAAS 2.8, we have made a number of performance improvements to everything related to the machine listing.  Some of the most visible changes involve the way that long lists are presented within categories (see the example below), but there are a number of other changes that make the list easier and more efficient to use.

![new-machine-list|690x357](upload://pOw7hKJZ2T7l7xXx8UycgBDFbvq.jpeg) 

Among those other changes are persisting UI state for grouping, new grouping options, bookmarkable URLs with filter and search parameters, and many other performance improvements. If you're interested in more details, see this [blog post](https://ubuntu.com/blog/building-a-cross-framework-ui-with-single-spa-in-maas-2-8).

## Support for using an external/remote PostgreSQL MAAS database with the snap version of MAAS

In order to make MAAS more scalable, we have separated the MAAS database from the MAAS snap, so that the DB can be situated separately.  MAAS 2.8 now allows the MAAS DB to be located outside the snap on localhost, or on a separate, external or remote server.  We complement this capability with extensive instructions for setting up and managing this configuration.  To support those who are testing MAAS, we've also provided a test DB configuration that embeds the database in a separate snap that can easily be connected to MAAS.

<h2 id="heading--bug-fixes">Bug fixes</h2>

We've also fixed number of bugs (see the [list in Launchpad](https://bugs.launchpad.net/bugs/+bugs?field.milestone%3Alist=89978&field.milestone%3Alist=90576&field.milestone%3Alist=90599&field.milestone%3Alist=90640&field.milestone%3Alist=90645&field.milestone%3Alist=90722&field.milestone%3Alist=91005&field.milestone%3Alist=91123&field.milestone%3Alist=91124&field.milestone%3Alist=91180&field.status%3Alist=FIXRELEASED)).  Notable among these are the following:

- [MAAS event table](https://bugs.launchpad.net/maas/+bug/1860619): Power events are now being logged differently to reduce log sizes and improve performance.

- [Unprivileged users controlling services](https://bugs.launchpad.net/maas/+bug/1864201): Unprivileged users can no longer start, stop, or restart services via HTTP channels.

- [Adding KVMs to snap-installed MAAS](https://bugs.launchpad.net/maas/+bug/1852405): SSH key usage has been updated so that KVMs can now be added to snap-installed MAAS without difficulty.

- [Trouble editing physical interfaces in GUI](https://bugs.launchpad.net/maas/+bug/1864241): It is now possible to edit physical interface parameters, when appropriate, from the web UI.

- [Subnet pages slow to load](https://bugs.launchpad.net/maas/+bug/1873430): Subnet pages now load more quickly and efficiently.

- [Trouble loading multiple MAC addresses](https://bugs.launchpad.net/maas/+bug/1865122): You can now reliably load multiple MAC addresses using the web UI.

- [Disabling DNS on regiond subnet breaks DNS](https://bugs.launchpad.net/maas/+bug/1871584): This problem has been resolved.

<h2 id="heading--2-8-known-issues">Known issues</h2>

* **Browser caching issue:** There is a known issue with browser caching on some MAAS pages.  If you initially encounter a page which does not appear to be correctly formatted, please manually clear your browser cache (**not Ctrl-F5**) and it should restore the page to normal.  You manually clear your browser cache, for example, in the "History" section of the menu on a Chrome browser.

* **Extra power types when adding chassis:** ([see bug report](https://bugs.launchpad.net/maas/+bug/1883743)) When adding a chassis, the "Power type" dropdown will show power types not supported by a chassis.  Selecting one of the non-supported power types will result in the UI blocking the action.  Here is a list of power types supported for chassis creation:
** `mscm` - Moonshot Chassis Manager
** `msftocs` - Microsoft OCS Chassis Manager
** `powerkvm` - Virtual Machines on Power KVM, managed by Virsh
** `recs_box` - Christmann RECS|Box servers
** `sm15k` - Seamicro 1500 Chassis
** `ucsm` - Cisco UCS Manager
** `virsh` - virtual machines managed by Virsh
** `vmware` - virtual machines managed by VMware

* **MAAS keys count in user list is bogus:** ([see bug report](https://bugs.launchpad.net/maas/+bug/1884112)) The count of keys shown in the User list in the UI is wrong.

* **Leftover lock files may be present under some conditions:** Even if you purge an old MAAS debian package, it can leave lock files in `/run/lock/maas*`.  This can cause issues if you later reinstall MAAS, and the previous MAAS user UID has been reassigned.  At that point, MAAS can't remove those files and create new ones.  If this occurs, it is easily fixed by removing those files manually before reinstalling.