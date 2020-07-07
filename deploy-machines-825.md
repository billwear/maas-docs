Once a machine has been commissioned (see [Commission machines](/t/commission-nodes/822)) the next logical step is to deploy it. Deploying a machine means, effectively, to install an operating system on it.

#### Quick questions you may have:

* [How and why do I acquire a machine?](/t/deploy-machines/825#heading--acquire)
* [How do I deploy a machine?](/t/deploy-machines/825#heading--deploy)

The agent that triggers deployment may vary. For instance, if the machines are destined to run complex, inter-related services that scale up or down frequently, like a "cloud" resource, then [Juju](https://jujucharms.com/docs/stable/about-juju) is the recommended deployment agent. Juju will also install and configure services on the deployed machines. If you want to use MAAS to install a base operating system and work on the machines manually, then you can deploy a machine directly with MAAS.

Machines deployed with MAAS will also be ready to accept connections via SSH, to the 'ubuntu' user account.  This connection assumes that you have imported an SSH key has to your MAAS account. This is explained in [SSH keys](/t/user-accounts/790#heading--ssh-keys).

[note]
Juju adds SSH keys to machines under its control.
[/note]

MAAS also supports machine customisation with a process called "preseeding." For more information about customising machines, see [Custom machine setup](/t/custom-node-setup-preseed/824).

<h2 id="heading--acquire">Acquire</h2>

Acquiring ("allocating") a machine reserves the machine for the exclusive use of the acquiring process. The machine is no longer available to any other process, including another MAAS instance, or a process such as [Juju](https://jujucharms.com/docs/stable/about-juju).

Before deploying a machine, MAAS must acquire it (status 'Allocated'). When deploying from the web UI, this action is performed automatically (and invisibly).

The action remains useful in terms of reserving a machine for later use. To acquire a machine explicitly select the machine and apply the 'Acquire' action.

See [MAAS CLI](/t/common-cli-tasks/794#heading--acquire-a-node) for how to acquire a machine with the CLI.

<h2 id="heading--deploy">Deploy</h2>

To deploy, you must configure the underlying machine to netboot.  Such a machine will undergo the following process:

1.  DHCP server is contacted
2.  kernel and initrd are received over TFTP
3.  machine boots
4.  initrd mounts a Squashfs image ephemerally over HTTP
5.  cloud-init triggers deployment process
    1.  curtin installation script runs
    2.  Squashfs image (same as above) is placed on disk

[note]
The curtin installer uses an image-based method and is now the only installer used by MAAS. Although the older debian-installer method has been removed, curtin continues to support preseed files. For more information about customising machines see [Custom machine setup](/t/custom-node-setup-preseed/824).
[/note]

Before deploying, you should:

-   review and possibly set the [Ubuntu kernels](/t/ubuntu-kernels/828) and the [Kernel boot options](/t/kernel-boot-options/827) that will get used by deployed machines.
-   ensure any pertinent SSH keys are imported (see [SSH keys](/t/user-accounts/790#heading--ssh-keys)) to MAAS so it can connect to deployed machines.

To deploy directly from MAAS, select one or more machine(s) and press the 'Deploy' button.

![deploy](https://assets.ubuntu.com/v1/56958753-nodes-deploy__2.4_deploy.png)

You then have the option of deviating from the default OS, release, and kernel. When ready, press 'Deploy X machine(s)' (where 'X' is the number of machines selected).

![confirm deploy](https://assets.ubuntu.com/v1/d65b9884-nodes-deploy__2.6-deploy-confirm.png)

See [MAAS CLI](/t/common-cli-tasks/794#heading--deploy-a-node) for how to deploy a machine with the CLI. If doing so, you will first need to acquire the machine explicitly, either via the web UI or the CLI.

While a machine is deploying its status will change to Deploying to 'OS', where 'OS' is the name of the OS being deployed (e.g. 'Deploying to Ubuntu 16.04 LTS').

Once a machine has finished deploying its status will change to just the name of the OS (e.g. 'Ubuntu 18.04 LTS').

<!-- LINKS -->