MAAS 2.5 and above can deploy VMware ESXi as a custom image. MAAS cannot directly deploy the VMware ESXi ISO; you must create a specialised image from an official VMWare ISO. Canonical created a [packer](https://www.packer.io/) template to automate the image creation process.

#### Quick questions you may have:

* [What are the prerequisite requirements for creating an image?](/t/vmware-images/753#heading--prerequisites-to-create-the-images)
* [How can I customize these images?](/t/vmware-images/753#heading--customizing-the-image)
* [How do I build an image?](/t/vmware-images/753#heading--building-an-image)
* [How do I upload an image?](/t/vmware-images/753#heading--uploading-an-image)
* [What are the features and limitations of VMWare images in MAAS?](/t/vmware-images/753#heading--features-and-limitations)

<h2 id="heading--prerequisites-to-create-the-images">Prerequisites to image creation</h2>

<h3 id="heading--image-creation">Image creation</h3>

-   A physical machine running Ubuntu 18.04+
-   **CPU**: 4 2GHz cores
-   **Memory**: 8 GB RAM (16 GB RAM recommended)
-   **Disk space**: 11 GB
-   [The VMWare ESxi ISO](https://my.vmware.com/en/web/vmware/evalcenter?p=free-esxi6)
-   [Packer - https://www.packer.io/intro/getting-started/install.html](https://www.packer.io/intro/getting-started/install.html)
-   Procedure was tested with precompiled 64-bit Packer 1.3.4 Linux binaries
-   <a href="https://github.com/canonical/packer-maas">Packer template</a> for MAAS custom image

<h3 id="heading--image-deployment">Image deployment</h3>

-   MAAS 2.5.0+

<h2 id="heading--customizing-the-image">Customizing the Image</h2>

The image may be customized by modifying packer-maas/vmware-esxi/http/vmware-esxi-ks.cfg see Installation and Upgrade Scripts in the [VMware ESXi installation and Setup manual](https://docs.vmware.com/en/VMware-vSphere/6.7/vsphere-esxi-67-installation-setup-guide.pdf) for more information.

<h2 id="heading--building-an-image">Building an Image</h2>

Before an image is built the nbd kernel module must be loaded

    sudo modprobe nbd

Once the nbd kernel module is loaded your current working directory must be in the packer-maas/vmware-esxi directory

    cd /path/to/packer-maas/vmware-esxi

You can now start the image building process using packer with the following command:

    sudo packer build -var
    'vmware_esxi_iso_path=/path/to/VMware-VMvisor-Installer-6.7.0-8169922.x86_64.iso'
    vmware-esxi.json

<h2 id="heading--uploading-an-image">Uploading an Image</h2>

Once you have created the image, upload it to MAAS, using the CLI, with the following command:

    maas $PROFILE boot-resources create name='esxi/6.7' title='VMware ESXi 6.7'
    architecture='amd64/generic' filetype='ddgz' content@=vmware-esxi.dd.gz

<h2 id="heading--features-and-limitations">Features and Limitations</h2>

<h3 id="heading--networking">Networking</h3>

-   VMware ESXi does not support Linux bridges
-   Bonds - MAAS maps the following bond modes to VMware ESXi NIC team sharing with load balancing as follows:
-   balance-rr - portid
-   active-backup - explicit
-   802.3ad - iphash, LACP rate and XMIT hash policy settings are ignored.
-   No other bond modes are currently supported.
-   VMware ESXi does not allow VMs to use a PortGroup that has a VMK attached to it. All configured devices will have a VMK attached. To use a vSwitch with VMs, you must leave a device or alias unconfigured in MAAS.

<h3 id="heading--storage">Storage</h3>

Custom storage configuration is not supported because VMware ESXi expects specific disk formats. MAAS will extend datastore1 to the full size of the deployment disk. After deployment, VMware tools may be used to access the other disks.

<h3 id="heading--esxi-hardware-support">ESXi Hardware Support</h3>

VMware has [very specific hardware requirements](https://www.vmware.com/resources/compatibility/search.php). In particular, running VMware ESXi is not supported in a virtual machine or MAAS virsh Pod.