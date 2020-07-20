<table width="100%"><tr>
<td><a href="heading--erasure-types">Erasure types</a></td>
<td><a href="heading--default-configuration">Default configuration</a></td>
</tr><tr>
<td><a href="heading--order-of-preference">Order of preference</a></td>
<td>&nbsp;</td>
</tr></table>
<hr>

Disk erasure pertains to the erasing of data on each of a machine's disks when the machine has been released (see [Release action](/t/concepts-and-terms/785#heading--release)) back into the pool of available machines. The user can choose from among three erasure types before confirming the Release action. A default erasure configuration can also be set.

<h2 id="heading--erasure-types">Erasure types</h2>

The three types of erasure types are:

-   Standard erase
-   Secure erase
-   Quick erase

Each of these are explained below.

<h3 id="heading--standard-erase">Standard erase</h3>

Overwrites all data with zeros.

<h3 id="heading--secure-erase">Secure erase</h3>

Although effectively equivalent to Standard erase, Secure erase is much faster because the disk's firmware performs the operation. Because of this, however, some disks may not be able to perform this erasure type (SCSI, SAS, and FC disks in particular).

<h3 id="heading--quick-erase">Quick erase</h3>

Same as Standard erase but only targets the first 1 MB and the last 1 MB of each disk. This removes the partition tables and/or superblock from the disk, making data recovery difficult but not impossible.

<h2 id="heading--default-configuration">Default configuration</h2>

A default erasure configuration can be set on the 'Settings' page by selecting the 'Storage' tab.

![disk erasure default settings](https://assets.ubuntu.com/v1/4e90c4c7-installconfig-storage-erasure__defaults.png)

If option 'Erase machines' disks prior to releasing' is chosen then users will be compelled to use disk erasure. That option will be pre-filled in the machine's view and the user will be unable to remove the option.

With the above defaults, the machine's view will look like this when the Release action is chosen:

![disk erasure default node view](https://assets.ubuntu.com/v1/66e1dcc2-installconfig-storage-erasure__defaults-node.png)

Where 'secure erase' and 'quick erase' can then be configured by the user.

<h2 id="heading--order-of-preference">Order of preference</h2>

If all three options are checked when the machine is released the following order of preference is applied:

1.  Use 'secure erase' if the disk supports it
2.  If it does not then use 'quick erase'

<!-- LINKS -->