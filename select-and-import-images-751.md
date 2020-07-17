MAAS is only useful once it has images available to provision its nodes. Therefore, one key post-install task is to select and import images from the boot source. Once MAAS imports images, it will update them on an hourly basis, via a default synch mechanism.  This page explains how to select and import the images that MAAS requires to provision its nodes.

#### Quick questions you may have:

* [How do I import standard images from maas.io?](/t/select-and-import-images/751#heading--import-maasio-image-ui)
* [How do I use other image mirrors to download images?](/t/select-and-import-images/751#heading--image-mirrors)
* [How do I import and provision non-Ubuntu images?](/t/select-and-import-images/751#heading--other-images)
* [How can I use the CLI to select and import images?](/t/cli-image-management/797#heading--select-images)

Note that it is possible to build your own images in a limited sense: see [MAAS Image Builder](/t/maas-image-builder/1112).

<h2 id="heading--import-maasio-image-ui">Importing images from maas.io</h2>

The 'Images' page shows what images and architectures have been selected and downloaded. By default, MAAS will automatically grab the most recent Ubuntu LTS releases (and amd64 architecture). Below, we have selected two additional releases:

![select and import images](https://discourse.maas.io/uploads/default/optimized/1X/d208922f1126ec92f6ef06cfaa5e16dbbfc613d0_2_690x462.png)

You can tell MAAS to sync images hourly, at the region level, using a toggle switch in the top-right corner of the screen.  See [Boot image sources](/t/images/754#boot-image-sources)) for more details. We highly recommended synching images hourly. Syncing at the rack controller level (from regiond) occurs every 5 min and cannot be disabled.

Click the 'Save selection' button to initiate the import. MAAS will present existing images along with the newly-selected ones. The latter will have their status updated as the import is processed:

![ubuntu images importing](https://discourse.maas.io/uploads/default/optimized/1X/f7daa92c97f1ada61c2172044d43856ed3e14b5f_2_690x139.png)

To remove an image, unselect it and click 'Save selection'.

<h2 id="heading--image-mirrors">Image mirrors</h2>

You can also host Ubuntu images on a mirror. Configure this mirror by selecting 'Custom' beneath 'Choose source'. Enter the mirror URL and click 'Connect'.

Advanced options, such as using a GPG key or keyring to validate the mirror path (snap installation location: /snap/maas/current/usr/share/keyrings/ubuntu-cloudimage-keyring.gpg), are revealed by clicking 'Show advanced options':

![image mirror configuration](https://discourse.maas.io/uploads/default/original/1X/dbe44a827e70e318a6139c3e335019a6a27c4374.png)

Optionally, a local mirror can be set up as the boot source. MAAS will then use it instead of the standard internet-based server. Local mirroring significantly reduces the time required import images. See [Local image mirror](/t/local-image-mirror/752) for instructions.

<h2 id="heading--other-images">Other images</h2>

It is also possible to import and provision images other than Ubuntu. Images supported and provided by MAAS will appear beneath the 'Other Images' section. Currently, images for CentOS 6.6, CentOS 7.0, and CentOS 8.0 are available. These images can be imported and used just like the Ubuntu images above.

![other images importing](https://discourse.maas.io/uploads/default/optimized/1X/198aa78b2dd3a650f1b3909ae2c9269e159ca1dc_2_690x182.png)

<!-- LINKS -->