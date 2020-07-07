MAAS Image Builder is an alternative to [packer](https://www.packer.io/) for creating MAAS images.  You can:

* <a href="#imib">install MAAS Image Builder</a> via a private Canonical PPA (which you can request)
* <a href="#bci">create custom CentOS images</a>
* <a href="#bri">create custom RHEL images</a>
* <a href="#bwi">create Windows images</a>
* <a href="#doci">create other kinds of custom images</a>

You can customize most images as much or as little as you wish, then use them to commission machines with MAAS. 

<a name="imib"><h3>Install MAAS Image Builder</h3></a>

To get MAAS Image Builder, you must be subscribed to a private PPA provided by Canonical Support.  Once subscribed, you need to obtain your credentials at:

https://launchpad.net/~/+archivesubscriptions

Also, you must add the repository with the <code>add-apt-repository</code> command.  Note: Be sure to substitute your unique URL in the command below:

    $ sudo add-apt-repository \
    “https://LaunchpadID:Password@private-ppa.launchpad.net/maas-image-builder-partners/stable/ubuntu"

Once you have added the private PPA, you can install the Image Builder like this:

    $ sudo apt-get install maas-image-builder

All done? Great!  Now you can build and customize images for MAAS machines, as shown in the sections below.

<a name="bci"><h3>Create custom CentOS images</h3></a>

MAAS already provides the latest available CentOS 6, CentOS 7, and CentOS 8 for automatic download. If you need something else, though, MAAS Image Builder supports the ability to create various CentOS images.

<h4>Network Requirements</h4>

Access to the Internet is required, since you will need to start with one of these:

 - https://mirror.centos.org  - OS, updates, and extra repositories
 - https://download.fedoraproject.org  - EPEL
 - https://copr-be.cloud.fedoraproject.org - Canonical maintained cloud-init repository

<h4>Creating images behind a proxy</h4>

MAAS Image Builder can create CentOS images behind a proxy -- just set the ‘http_proxy’ environment variable to _your_ particular proxy. Once deployed, <code>yum</code> will use this MAAS-configured proxy.

<h4>Creating the images</h4>

<code>maas-image-builder</code> is designed to automate the process of generating the images for MAAS and <code>curtin</code>.  Here are some specific examples:

    $ sudo maas-image-builder -o centos6-amd64-root-tgz --arch amd64 centos --edition 6 
    $ sudo maas-image-builder -o centos6-i386-root-tgz --arch i386 centos --edition 6
    $ sudo maas-image-builder -o centos7-amd64-root-tgz --arch amd64 centos --edition 7

<h4>Customizing CentOS images</h4>

Starting from MAAS Image Builder 1.0.4, customization of CentOS images is now supported.  You can provide a custom kickstart, in _addition_ to the kickstart that MAAS Image Builder uses to create the images. You can customize your image like this:

    $ sudo maas-image-builder -o centos7-amd64-root-tgz --arch amd64 centos --edition 7 --custom-kickstart ./custom.ks

<h4>Uploading the image into MAAS</h4>

Custom CentOS images can be uploaded to MAAS as shown in the command below.  _Do note_ that the name **must** start with ‘centos’ and **must** be one line:

    maas admin boot-resources create name=centos/centos6-custom architecture=amd64/generic content@=./build-output/centos-amd64-root-tgz

You can use the MAAS WebUI to check that your custom CentOS image is valid and selectable for deployment.

<a name="bri"><h3>Building RHEL images</h3></a>

Currently, MAAS _only_ supports RHEL as a custom image. In future versions of MAAS, RHEL will be natively supported.

<h4>Some Requirements</h4>

In order to create RHEL images, you will need access to:

* A RHEL DVD ISO - Contains all RHEL archives which are not available without a license
* https://download.fedoraproject.org - Access to the EPEL repository to install required deps
* https://copr-be.cloud.fedoraproject.org - Access to the Canonical maintained cloud-init copr repository

<h4>Creating images behind a proxy</h4>

MAAS image builder supports creating RHEL images behind a proxy. To use a proxy when building a RHEL image, just set the ‘http_proxy’ environment variable to _your_ local proxy. Once deployed, <code>yum</code> will use the MAAS-configured proxy.

<h4>Creating the images</h4>

To generate a usable RHEL image, <code>maas-image-builder</code> automates image generation; these images can be used by MAAS and <code>curtin</code>.

     $ sudo maas-image-builder -a amd64 -o rhel7-amd64-root-tgz rhel --rhel-iso blah.iso

<h4>Install into MAAS</h4>

The custom RHEL image can be uploaded to MAAS, but note that the name **must** start with ‘rhel’ and **must** be expressed as a single line, like this:

    maas admin boot-resources create name=rhel/7 title="RedHat Enterprise Linux 7" architecture=amd64/generic content@=rhel7-amd64-root-tgz

<a name="bwi"><h3>Building Windows Images</h3></a>

Since Windows is a proprietary operating system, MAAS can't download these images. You need to manually generate images to use with MAAS.  On the upside, the end result will be much simpler, since there are CLI and WebUI tools to upload a Windows ISO -- which _helps_ automate the process.

<b>Hyper-V 2012 R2</b>
In this example, Windows Hyper-V 2012 R2 is used, but rest assured that multiple versions are supported. Download the Hyper-V 2012 R2 ISO from Microsoft, so it can be used in the image generation process.  You can obtain the download at:

http://technet.microsoft.com/en-US/evalcenter/dn205299.aspx

Other supported versions include (for --windows-edition):

* win2012
* win2012r2
* win2012hv
* win2012hvr2
* win2016
* win2016hv

<h4>Image Builder</h4>

MAAS Image builder can automate the process of generating images for MAAS and <code>curtin</code>.  In this instance,  though, you need Windows drivers for your specific hardware.  You can obtain these windows drivers with the following command:

    sudo maas-image-builder -o windows-win2012hvr2-amd64-root-dd  windows \
    --windows-iso ~/Downloads/2012hvr2.ISO \
    --windows-edition win2012hvr2 [--windows-updates] \
    [--windows-drivers <folder/>]  [--windows-language en-US]

As an example, consider:

    sudo maas-image-builder -o windows-win2012hvr2-amd64-root-dd windows \
    --windows-iso win2012hvr2.iso  --windows-edition win2012hvr2 \
    --windows-updates --windows-drivers ~/Win2012hvr2_x64/DRIVERS/  --windows-language en-US

Please note that this will not <em>install</em> any Windows updates. In order to obtain an up-to-date image of Windows, be sure provide the <code>--windows-updates</code> flag. This requires access to a bridged connection with a DHCP server, an interface that can be specified with <code>-i</code>.

Also note that you may be required to have specific Windows drivers for this image to work in your hardware. Be sure you inject those drivers when installing them. Those drivers are the default <code>*.inf</code> files.

<h4>Debug</h4>

You can debug the Windows installation process by connecting to <code>localhost:5901</code> using a VNC client.

<h4>Install into MAAS</h4>

The generated images need to be placed into the correct directories so MAAS can deploy them onto a node:

    maas admin boot-resources create name=windows/win2012hvr2 \
    architecture=amd64/generic filetype=ddtgz \ 
    content@=./build-output/windows-win2012hvr2-amd64-root-dd 

Now, using the MAAS WebUI, a node can be selected to use Windows Hyper-V 2012 R2. This selection gets reset when a node is stopped, so make sure to set it _before_ starting nodes. You can also set the default operating system (and release) in the settings menu, which removes the need to set it per-node.

<a name="doci"><h3>Dealing with other custom images</h3></a>

To install other custom images, use the following command sequence:

    maas <user> boot-resources create name=<custom-image-codename> title="Ubuntu Custom Image" \
    architecture=amd64/generic content@=/location/of/custom/image/ubuntu-custom-root-tgz

As an example:

    maas admin boot-resources create name=custom1 \
    title=”Ubuntu Custom Image” architecture=amd64/generic \
    content@=/home/ubuntu/ubuntu-custom-root-tgz