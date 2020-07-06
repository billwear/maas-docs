During machine [enlistment](/t/add-nodes/821#heading--enlistment), [deployment](/t/deploy-nodes/825), [commissioning](/t/commission-nodes/822) and machine installation, MAAS sends [Tempita-derived](https://raw.githubusercontent.com/ravenac95/tempita/master/docs/index.txt) configuration files to the [cloud-init](https://launchpad.net/cloud-init) process running on the target machine. MAAS refers to this process as **preseeding**.These preseed files are used to configure a machine's ephemeral and installation environments and can be modified or augmented to a custom machine configuration.

#### Quick questions you may have:

* [How do I customize machine setup with curtin?](/t/custom-machine-setup/824#heading--curtin)
* [How do I customize machine setup with cloud-init?](/t/custom-machine-setup/824#heading--cloud-init)

Customisation in MAAS happens in two ways:

1.  [Curtin](https://launchpad.net/curtin), a preseeding system similar to Kickstart or d-i (Debian Installer), applies customisations during operating system (OS) image installation. MAAS performs these changes on deployment, during OS installation, but before the machine reboots into the installed OS. Curtin customisations are perfect for administrators who want their deployments to have identical setups all the time, every time. [This blog post](https://blog.ubuntu.com/2017/06/02/customising-maas-installs) contains an excellent high-level overview of custom MAAS installs using Curtin.
2.  [Cloud-init](https://launchpad.net/cloud-init), a system for setting up machines immediately after instantiation. Curtin applies customisations after the first boot, when MAAS changes a machine's status to 'Deployed.' Customisations are per-instances, meaning that user-supplied scripts must be re-specified on redeployment. Cloud-init customisations are the best way for MAAS users to customise their deployments, similar to how the various cloud services prepare VMs when launching instances.

<h2 id="heading--curtin">Curtin</h2>

<h3 id="heading--templates">Templates</h3>

The [Tempita](https://raw.githubusercontent.com/ravenac95/tempita/master/docs/index.txt) template files are found within the `/etc/maas/preseeds/` directory on the region controller. Each template uses a filename prefix that corresponds to a particular phase of MAAS machine deployment:

|       Phase       |                 Filename prefix                 |
|:-----------------:|:-----------------------------------------------:|
|   1\. Enlistment  |                      enlist                     |
| 2\. Commissioning |                  commissioning                  |
|  3\. Installation | curtin ([Curtin](https://launchpad.net/curtin)) |

Additionally, the template for each phase typically consists of two files. The first is a higher-level file that often contains little more than a URL or a link to further credentials, while a second file contains the executable logic.

The `enlist` template, for example, contains only minimal variables, whereas `enlist_userdata` includes both user variables and initialisation logic.

[note]
Tempita’s inheritance mechanism is the reverse of what you might expect. Inherited files, such as `enlist_userdata`, become the new template which can then reference variables from the higher-level file, such as `enlist`.
[/note]

<h3 id="heading--template-naming">Template naming</h3>

MAAS interprets templates in lexical order by their filename.  This order allows for base configuration options and parameters to be overridden based on a combination of operating system, architecture, sub-architecture, release, and machine name.

Some earlier versions of MAAS only support Ubuntu. If the machine operating system is Ubuntu, then filenames without `{os}` will also be tried, to maintain backward compatibility.

Consequently, template files are interpreted in the following order:

1.  `{prefix}_{os}_{node_arch}_{node_subarch}_{release}_{node_name}` or `{prefix}_{node_arch}_{node_subarch}_{release}_{node_name}`

2.  `{prefix}_{os}_{node_arch}_{node_subarch}_{release}` or `{prefix}_{node_arch}_{node_subarch}_{release}`

3.  `{prefix}_{os}_{node_arch}_{node_subarch}` or `{prefix}_{node_arch}_{node_subarch}`

4.  `{prefix}_{os}_{node_arch}` or `{prefix}_{node_arch}`

5.  `{prefix}_{os}`

6.  `{prefix}`

7.  `generic`

The machine needs to be the machine name, as shown in the web UI URL.

The prefix can be either `enlist`, `enlist_userdata`, `commissioning`, `curtin`, `curtin_userdata` or `preseed_master`. Alternatively, you can omit the prefix and the following underscore.

For example, to create a generic configuration template for Ubuntu 16.04 Xenial running on an x64 architecture, the file would need to be called `ubuntu_amd64_generic_xenial_node`.

To create the equivalent template for curtin_userdata, the file would be called `curtin_userdata_ubuntu_amd64_generic_xenial_node`.

[note]
Any file targetting a specific machine will replace the values and configuration held within any generic files. If those values are needed, you will need to copy these generic template values into your new file.
[/note]

<h3 id="heading--configuration">Configuration</h3>

You can customise the Curtin installation by either editing the existing `curtin_userdata` template or by adding a custom file as described above.

Curtin provides hooks to execute custom code before and after installation takes place. These hooks are named `early` and `late` respectively, and they can both be overridden to execute the Curtin configuration in the ephemeral environment. Additionally, the `late` hook can be used to execute a configuration for a machine being installed, a state known as in-target.

Curtin commands look like this:

    foo: ["command", "--command-arg", "command-arg-value"]

Each component of the given command makes up an item in an array. Note, however, that the following won't work:

    foo: ["sh", "-c", "/bin/echo", "foobar"]

This syntax won't work because the value of `sh`'s `-c` argument is itself an entire command. The correct way to express this is:

    foo: ["sh", "-c", "/bin/echo foobar"]

The following is an example of an early command that will run before the installation takes place in the ephemeral environment. The command pings an external machine to signal that the installation is about to start:

``` bash
early_commands:
  signal: ["wget", "--no-proxy", "http://example.com/", "--post-data", "system_id=&signal=starting_install", "-O", "/dev/null"]
```

The following is an example of two late commands that run after installation is complete. Both run in-target, on the machine being installed.

The first command adds a PPA to the machine. The second command creates a file containing the machine’s system ID:

``` bash
late_commands:
  add_repo: ["curtin", "in-target", "--", "add-apt-repository", "-y", "ppa:my/ppa"]
  custom: ["curtin", "in-target", "--", "sh", "-c", "/bin/echo -en 'Installed ' > /tmp/maas_system_id"]
```

<h2 id="heading--cloud-init">Cloud-init</h2>

Using cloud-init to customise a machine after deployment is relatively easy. If you're not familiar with the MAAS command-line interface (CLI), start by reviewing the [MAAS CLI](/t/maas-cli/802) page.

After you're logged in, use the following command to deploy a machine with a custom script you've written:

    maas $PROFILE machine deploy $SYSTEM_ID user_data=<base-64-encoded-script>

-   `$PROFILE`: Your MAAS login. E.g. `admin`
-   `$SYSTEM_ID`: The machine's [system ID](/t/common-cli-tasks/794#heading--determine-a-node-system-id).
-   `<base-64-encoded-script>`: A base-64 encoded copy of your customisation script. See below for an example.

E.g.:

Suppose you would like to import an SSH key immediately after your machine deployment. You might use this script, called `import_key.sh`:

``` bash
#!/bin/bash
(
echo === $date ===
ssh-import-id foobar_user
) | tee /ssh-key-import.log
```

This script echos the date in addition to the output of the `ssh-import-key` command. It also adds that output to a file, `/ssh-key-import.log`.

Base-64 encoding is required because the MAAS command-line interacts with the MAAS API, and base-64 encoding allows MAAS to send the script inside a POST HTTP command.

Use the `base64` command to output a base-64 encoded version of your script:

    base64 ./import_key.sh

Putting it together:

    maas $PROFILE machine deploy $SYSTEM_ID user_data=$(base64 ./import_key.sh)

After MAAS deploys the machine, you'll find `/ssh-key-import.log` on the machine you deployed.

<!-- LINKS -->