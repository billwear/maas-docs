MAAS can specify kernel boot options to machines on both a global basis and a per-machine basis. A full catalog of available options can be found in the [Linux kernel parameters list](https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html) at kernel.org.

#### Quick questions you may have:

* [How can I set global kernel boot options for all machines?](/t/kernel-boot-options/827#heading--global-kernel-boot-options)
* [How can I set kernel boot options for a specific machine?](/t/kernel-boot-options/827#heading--per-node-kernel-boot-options)

<h2 id="heading--global-kernel-boot-options">Global kernel boot options</h2>

To set kernel boot options globally, as an admin, open the 'Settings' page and on the 'General' tab scroll down to the 'Global Kernel Parameters' section:

![global kernel options](https://assets.ubuntu.com/v1/8b793b6d-nodes-kernel-options__2.2_global.png)

Type in the desired (space separated) options and click 'Save'. The contents of the field will be used as-is. Do not use extra characters.

<h3 id="heading--cli">CLI</h3>

You can set kernel boot options and apply them to all machines with the CLI command:

``` bash
maas $PROFILE maas set-config name=kernel_opts value='$KERNEL_OPTIONS'
```

<h2 id="heading--per-node-kernel-boot-options">Per-machine kernel boot options</h2>

Per-machine kernel boot options are set using the CLI.

[note]
Per-machine boot options take precedence to global ones.
[/note]

To specify kernel boot options for an individual machine, first create a tag:

``` bash
maas $PROFILE tags create name='$TAG_NAME' \
    comment='$COMMENT' kernel_opts='$KERNEL_OPTIONS'
```

For example:

``` bash
maas $PROFILE tags create name='nomodeset' \
    comment='nomodeset kernel option' kernel_opts='nomodeset vga'
```

Next, assign the tag to the machine in question:

``` bash
maas $PROFILE tag update-nodes $TAG_NAME add=$SYSTEM_ID
```

If multiple tags attached to a machine have the `kernel_opts` defined, MAAS uses the first one found, in alphabetical order.

See the [CLI tag management](/t/cli-tag-management/801) section for more information about using the CLI to manage tags.

<!-- LINKS -->