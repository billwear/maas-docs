The following is a list of kernel tasks that you can perform with the MAAS CLI.

#### Quick questions you may have:

* [What should I know about Ubuntu kernels on MAAS?](/t/ubuntu-kernels/828)
* [What kernel boot options are available?](/t/kernel-boot-options/827)
* [How do I get started with the MAAS CLI?](/t/maas-cli/802)
* [How do I set a minimum kernel for enlistment and commisioning?](/t/cli-kernel-management/799#heading--set-a-default-minimum-kernel-for-enlistment-and-commissioning)
* [How to I set a minimum deploy kernel for a machine?](/t/cli-kernel-management/799#heading--set-a-minimum-deploy-kernel-for-a-machine)
* [How do I set a specific kernel during machine deployment?](/t/cli-kernel-management/799#heading--set-a-specific-kernel-during-machine-deployment)

<h2 id="heading--set-a-default-minimum-kernel-for-enlistment-and-commissioning">Set a default minimum kernel for enlistment and commissioning</h2>

To set a default minimum kernel for all new and commissioned machines:

``` bash
maas $PROFILE maas set-config name=default_min_hwe_kernel value=$KERNEL
```

For example, to set it to the 16.04 GA kernel:

``` bash
maas $PROFILE maas set-config name=default_min_hwe_kernel value=ga-16.04
```

[note]
The command option `default_min_hwe_kernel` appears to apply to only HWE kernels but this is not the case.
[/note]

<h2 id="heading--set-a-minimum-deploy-kernel-for-a-machine">Set a minimum deploy kernel for a machine</h2>

To set the minimum deploy kernel on a per-machine basis:

``` bash
maas $PROFILE machine update $SYSTEM_ID min_hwe_kernel=$HWE_KERNEL
```

For example, to set it to the HWE 16.04 kernel:

``` bash
maas $PROFILE machine update $SYSTEM_ID min_hwe_kernel=hwe-16.04
```

[note]
The command option `default_min_hwe_kernel` appears to apply to only HWE kernels but this is not the case.
[/note]

<h2 id="heading--set-a-specific-kernel-during-machine-deployment">Set a specific kernel during machine deployment</h2>

To set a specific kernel during the deployment of a machine:

``` bash
maas $PROFILE machine deploy $SYSTEM_ID distro_series=$SERIES hwe_kernel=$KERNEL
```

The operation will fail if the kernel specified by `hwe_kernel` is older than the kernel (possibly) given by `default_min_hwe_kernel`. Similarly, it will not work if the kernel is not available for the given distro series (such as 'hwe-16.10' for 'xenial').

For example, to deploy a Xenial node with the HWE 16.04 edge kernel:

``` bash
maas $PROFILE machine deploy $SYSTEM_ID distro_series=xenial hwe_kernel=hwe-16.04-edge
```

[note]
The command option `hwe_kernel` appears to apply to only HWE kernels but this is not the case.
[/note]

<!-- LINKS -->