MAAS supports four types of kernels for its Ubuntu machines.

-   General availability kernels
-   Hardware enablement kernels
-   Hardware enablement kernels (pre-release)
-   Low latency kernels

This article explains each of these kernels, and offers instructions and further references regarding their use.

#### Quick questions you may have:

* [What is a general availability kernel?](/t/ubuntu-kernels/828#heading--general-availability-kernels)
* [What is a hardware enablement kernel?](/t/ubuntu-kernels/828#heading--hardware-enablement-kernels)
* [What is a pre-release hardware enablement kernel?](/t/ubuntu-kernels/828#heading--hardware-enablement-kernels-pre-release)
* [What is a low latency kernel?](/t/ubuntu-kernels/828#heading--low-latency-kernels)
* [How can I choose and configure my preferred kernel?](/t/ubuntu-kernels/828#heading--using-kernels)

<h2 id="heading--general-availability-kernels">General availability kernels</h2>

The *general availability* (GA) kernel is based on the *generic* kernel that ships with a new Ubuntu version. Subsequent fixes are applied regularly by the 'daily' *stream* used when setting up the global image source for MAAS.

MAAS denotes a GA kernel like this:

`ga-<version>`: The GA kernel reflects the major kernel version of the shipped Ubuntu release. For example, 'ga-16.04' is based on the 'generic' 4.4 Ubuntu kernel. As per Ubuntu policy, a GA kernel will never have its major version upgraded until the underlying release is upgraded.

<h2 id="heading--hardware-enablement-kernels">Hardware enablement kernels</h2>

New hardware gets released all the time. If an Ubuntu host runs an older kernel, it's unlikely that MAAS can support the hardware. Canonical does make every effort to backport more recent kernels enabling more hardware. The acronym HWE stands for "HardWare Enablement."

You also gain kernel improvements and new features when installing an HWE kernel.

[note]
There is the notion of an HWE *stack*, which refers to the window manager and kernel when the Ubuntu host is running a desktop environment. HWE stacks do not apply to MAAS since machines are provisioned strictly as non-graphical servers.
[/note]

Note that these backported/HWE kernels are only available for LTS releases (e.g. Trusty, Xenial, etc.). For example, the first available HWE kernel for Ubuntu 16.04 LTS (Xenial) will be the GA kernel from Ubuntu 16.10 (Yakkety).

Before MAAS 2.1 on Xenial, HWE kernels are referred to by the notation `hwe-<release letter>`. So, to install the Yakkety HWE kernel on Xenial, the `hwe-y` kernel is used. By default, when using the web UI, MAAS imports all available HWE kernels along with its generic boot images. So if you are importinTrusty images are imported then the following HWE kernels are included: `hwe-u`, `hwe-v`, `hwe-w`, `hwe-x` (presuming the Xenial HWE kernel is available).

In MAAS 2.1, starting with Xenial kernels, the notation has changed. The following is used to refer to the latest HWE kernel available for Xenial: `hwe-16.04`.

See [MAAS CLI](/t/cli-image-management/797#heading--hardware-enablement-hwe) for how to target specific HWE kernels when selecting install images.

See [LTS Enablement Stack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack) (Ubuntu wiki) for the latest information on HWE.

<h2 id="heading--hardware-enablement-kernels-pre-release">Hardware enablement kernels (pre-release)</h2>

The pre-release HWE kernel is known as the *edge* HWE kernel.

MAAS denotes the edge kernel like this: `hwe-<version>-edge`.

So 'hwe-16.04' is considered older than 'hwe-16.04-edge'.

See [Rolling LTS Enablement Stack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack#hwe-16.04-edge) (Ubuntu wiki) for more information.

<h2 id="heading--low-latency-kernels">Low latency kernels</h2>

The low-latency kernel is based on the GA kernel, but uses a more aggressive configuration to reduce latency. It is categorised as a soft real-time kernel. For more information, see [Criteria for real-time computing](https://en.wikipedia.org/wiki/Real-time_computing#Criteria_for_real-time_computing) (Wikipedia).

MAAS denotes a low latency kernel in a few ways:

-   `hwe-x-lowlatency`: the Xenial low latency HWE kernel for Trusty
-   `ga-16.04-lowlatency`: the low latency GA kernel for Xenial
-   `hwe-16.04-lowlatency`: the low latency HWE kernel for Xenial

<h2 id="heading--using-kernels">Using kernels</h2>

The kernel installed on a machine during deployment is, by default, the Ubuntu release's native kernel (GA). However, it is possible to tell MAAS to use a different kernel. Via the Web UI, MAAS can help you choose one of these kernels.  There are three different contexts for your choice:

-   globally (default minimum enlistment and commissioning kernel)
-   per machine (minimum deploy kernel)
-   per machine during deployment (specific deploy kernel)

You can also use the [MAAS CLI](/t/cli-kernel-management/799#heading--set-a-default-minimum-kernel-for-enlistment-and-commissioning) to make these choices.

<h3 id="heading--default-minimum-kernel">Default minimum kernel</h3>

To set the default minimum enlistment and commissioning kernel (based on Ubuntu release: GA kernel) for all machines visit the 'General' tab of the 'Settings' page and select a kernel in the 'Default Minimum Kernel Version' field of the *Commissioning* section. Don't forget to click 'Save'.

![default minimum kernel](https://assets.ubuntu.com/v1/e0c7f298-nodes-kernels__2.6-default-minimum-kernel.png)

<h3 id="heading--machine-minimum-kernel">Machine minimum kernel</h3>

To set the minimum deploy kernel on a machine basis, click on a machine from the 'Machines' page of the web UI and switch to its 'Configuration' page. Click 'Edit' in the 'Machine configuration' section, select a kernel in the 'Minimum Kernel' field followed by 'Save changes'.

![machine minimum kernel](https://assets.ubuntu.com/v1/e1016632-nodes-kernels__2.6-machine-minimum-kernel.png)

<h3 id="heading--machine-kernel-during-deployment">Machine kernel during deployment</h3>

To set a specific kernel during deployment, select a machine from the 'Machines' page and choose 'Deploy' under 'Take action'. Then choose a kernel from the (third) kernel field. Hit 'Deploy machine' to initiate the deployment.

![machine during deploy kernel](https://assets.ubuntu.com/v1/0d25737f-nodes-kernels__2.6-machine-during-deploy-kernel.png)

MAAS verifies that the specified kernel is available for the given Ubuntu release (series) before deploying the machine.

<!-- LINKS -->