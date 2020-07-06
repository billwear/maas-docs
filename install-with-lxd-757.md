<!--
Todo:
- Text needs a review
-->
[LXD](https://linuxcontainers.org/lxd/) is a manager for Linux-based containers (LXC), offering a user experience similar to virtual machines without the same overheads.

Using LXD to install MAAS into containers is a good choice for users who want to test MAAS, or who may want to continue leveraging an existing container architecture or policy.

MAAS running with LXD has the following requirements:

-   a network bridge on the LXD host (e.g. lxdbr0)
-   LXD and ZFS
-   a container profile

<h3 id="heading--install-lxd-and-zfs">Install LXD and ZFS</h3>

Begin by installing LXD and ZFS:

``` bash
sudo apt install lxd zfsutils-linux
sudo modprobe zfs
sudo lxd init
```

The *sudo lxd init* command will trigger a series of configuration questions. Except in the case where the randomly chosen subnet may conflict with an existing one in your local environment, all questions can be answered with their default values.

The bridge network is configured via a second round of questions and is named *lxdbr0* by default.

<h3 id="heading--create-a-lxc-profile-for-maas">Create a LXC profile for MAAS</h3>

First create a container profile by making use of the 'default' profile:

``` bash
lxc profile copy default maas
```

Second, bind the network interface inside the container (eth0) to the bridge on the physical host (lxdbr0):

``` bash
lxc profile device set maas eth0 parent lxdbr0
```

Thirdly, the *maas* container profile needs to be edited to include a specific set of privileges. Enter the following to open the profile in your editor of choice:

``` bash
lxc profile edit maas
```

And replace the `{}` after *config* with the following (excluding `config:`):

``` yaml
config:
  raw.lxc: |-
    lxc.cgroup.devices.allow = c 10:237 rwm
    lxc.apparmor.profile = unconfined
    lxc.cgroup.devices.allow = b 7:* rwm
  security.privileged: "true"
```

The final step adds the 8 necessary loop devices to LXC:

``` bash
for i in `seq 0 7`; do lxc profile device add maas loop$i unix-block path=/dev/loop$i; done
```

When correctly configured, the above command outputs `Device loop0 added to maas` for each loop device.

<h3 id="heading--launch-and-access-the-lxd-container">Launch and access the LXD container</h3>

Launch the LXD container:

``` bash
lxc launch -p maas ubuntu:18.04 bionic-maas
```

Once the container is running, it can be accessed with:

``` bash
lxc exec bionic-maas bash
```

<h3 id="heading--install-maas">Install MAAS</h3>

In the container (or containers),[install MAAS](/t/maas-installation-from-a-snap/773).

<!-- LINKS -->