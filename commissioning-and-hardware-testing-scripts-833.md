MAAS runs scripts during enlistment, commissioning and testing to collect data about nodes. Both enlistment and commissioning run all builtin commissioning scripts, though enlistment runs only builtins. Commissioning also runs any user-uploaded commissioning scripts by default, unless the user manually provides a list of scripts to run. MAAS uses these commissioning scripts to configure hardware and perform other tasks during commissioning, such as updating the firmware. Similarly, MAAS employs hardware testing scripts to evaluate system hardware and report its status. 

---

<h4>Quick questions you may have:</h4>

* [What metadata fields exist, and how do they control the scripts?](/t/commissioning-and-hardware-testing-scripts/833#heading--metadata-fields)
* [What parameters can I use to control the scripts?](/t/commissioning-and-hardware-testing-scripts/833#heading--parameters)
* [ What environment variables are available to me?](/t/commissioning-and-hardware-testing-scripts/833#heading--environment-variables)
* [How can I control the results output from the scripts?](/t/commissioning-and-hardware-testing-scripts/833#heading--results)
* [What would some example scripts look like?](/t/commissioning-and-hardware-testing-scripts/833#heading--script-examples)
* [How do I upload scripts to MAAS?](/t/commissioning-and-hardware-testing-scripts/833#heading--upload-procedure)
* [How can I debug script issues and failures?](/t/commissioning-and-hardware-testing-scripts/833#heading--debugging)
* [How can I use these scripts via the command line?](/t/cli-testing-scripts/832)

---

Scripts can be selected to run from web UI [during commissioning](/t/commission-nodes/822), by [testing hardware](/t/hardware-testing/826) or from the [command line](/t/cli-testing-scripts/832). Note that MAAS only runs built-in commissioning scripts during enlistment. Custom scripts can be run when you explicitly choose to commission a machine.  A typical administrator workflow (with machine states), using customised commissioning scripts, can be represented as:

Add machine -&gt; Enlistment (runs built-in commissioning scripts MAAS) -&gt; New -&gt; Commission (runs built-in and custom commissioning scripts) -&gt; Ready -&gt; Deploy

NOTE: Scripts are run in alphabetical order in an ephemeral environment.  We recommend running your scripts after any MAAS built-in scripts.  This can be done by naming your scripts 99-z*.  It is possible to reboot the system during commissioning using a script, however, as the environment is ephemeral, any changes to the environment will be destroyed upon reboot (barring, of course, firmware type updates).

This article explains script metadata, parameter passing, and results-reporting.  It also offers examples of both commissioning and hardware testing scripts.
<h2 id="heading--metadata-fields">Metadata fields</h2>

Metadata fields tell MAAS when to use the script, how it should run, and what information it's gathering. A script can have the following fields:

-   `name`: The name of the script.
-   `title`: Human-friendly descriptive version of the name, used within the web UI.
-   `description`: Brief outline of what the script does.
-   `tags`: List of tags associated with the script.
-   `type`: Either commissioning or testing.
-   `timeout`: Length of time before MAAS automatically fails and kills execution of the script. The time may be specified in seconds or using the HH:MM:SS format.
-   `destructive`: True or False, depending on whether the script will overwrite system data. You can't run destructive tests on a deployed machine.
-   `comment`: Describes changes made in this revision of the script.  A comment can be passed via the API when uploading the script.  MAAS doesn’t look at the script metadata for this field.
-   `hardware_type`: Defines the type of hardware the script configures or tests. If the script returns results, hardware type associate the results with specific hardware. The following types are valid:
    -   `node`: Not associated with any hardware type; this is the default.
    -   `cpu`: Configures or tests the CPUs on the machine.
    -   `memory`: Configures or tests memory on the machine.
    -   `storage`: Configures or tests storage on the machine.
    -   `network`: Configures or tests network on the machine.
-   `parallel`: Enables scripts to be run in parallel and can be one of the following:
    -   `disabled`: The script will run serially on its own.
    -   `instance`: Runs in parallel only with other instances of the same script.
    -   `any`: Runs in parallel alongside any other scripts with parallel set to any.
-   `parameters`: What [parameters](#heading--parameters) the script accepts.
-   `results`: What [results](#heading--results) the script will return.
-   `packages`: List of packages to be installed or extracted before running the script. Packages must be specified as a dictionary. For example, `packages: {apt: stress-ng}`, would ask `apt` to install stress-ng. Package sources can be any of the following:
    -   `apt`: Use the Ubuntu apt repositories as configured by MAAS to install a package.
    -   `snap`: Installs packages using [snap][snapcraft]. May also be a list of dictionaries. The dictionary must define the name of the package to be installed, and optionally, the `channel`, `mode` and `revision`.
    -   `url`: The archive will be downloaded and, if possible, extracted or installed when a Debian package or [snap][snapcraft].
-   `for_hardware`: Specifies the hardware that must be on the machine for the script to run. May be a single string or list of strings of the following:
    -   `modalias`: Starts with 'modalias:' may optionally contain wild cards.
    -   `PCI ID`: Must be in the format of 'pci:VVVV:PPPP' where VVVV is the vendor ID, and PPPP is the product ID.
    -   `USB ID`: Must be in the format of 'usb:VVVV:PPPP' where VVVV is the vendor ID, and PPPP is the product ID.
    -   `System Vendor`: Starts with 'system_vendor:'.
    -   `System Product`: Starts with 'system_product:'.
    -   `System Version`: Starts with 'system_version:'.
    -   `Mainboard Vendor`: Starts with 'mainboard_vendor:'.
    -   `Mainboard Product`: Starts with 'mainboard_product:'.
-   `may_reboot`: When True, indicates to MAAS that the script may reboot the machine. MAAS will allow up to 20 minutes between heartbeats while running a script with `may_reboot` set to True.
-   `recommission`: After all commissioning scripts have finished running rerun
-   `script_type`: commissioning or test. Indicates whether the script should run during commissioning or hardware testing.

<h2 id="heading--parameters">Parameters</h2>

Scripts can accept exactly three types of parameters, and only three types: storage, interface, and URL.  The values of these parameters are strictly checked against existing disks (storage), working interfaces (interface), and valid URLs (URL)  No other types of information can be passed as parameters; they are not configured to pass user-specified data.

Parameters are automatically switched by MAAS to match the device being tested, to allow one test to be run against multiple devices at the same time while keeping separate logs.  For this reason, you may only specify parameters within the embedded YAML of the script, and they must take the form of a dictionary of dictionaries.

The key of the dictionary must be a string, and it's this string that's used by the UI and API when users are setting parameter values during commissioning or testing.  The value is a dictionary with the following fields:

-   `type`: Every parameter must contain a type field, which describes what the parameter may accept and its default values. It may be one of the following:
    -   `storage`: Allows the selection of a storage device on the currently-running machine.
    -   `interface`: Allows the selection of an interface on the currently-running machine.
    -   `url`: Allows the the passing of a valid URL.
-   `title`: The title of the parameter field when displayed in the UI. The following types have the following default values:
    -   `storage`: storage device.
    -   `interface`: interface specifer.
    -   `url`: valid URL.
-   `argument-format`: Specifies how the argument should be passed to the script. Input is described as `{input}`. The storage type may also use `{name}`, `{path}`, `{model}` or `{serial}`. MAAS will look up the values of path, model, and serial based on user selection. For storage, `{input}` is synonymous with `{path}`. The interface type may also use `{name}`, `{mac_address}`, `{product}`, or `{vendor}`. For interface `{input}` is synonymous with `{name}`. The following types have the following default values:
    -   `storage`: `--storage={path}`
    -   `interface`: `--interface={name}`
    -   `url`: `--url={input}`
-   `default`: The default value of the parameter. The following types have the following default values. Setting these to '' or None will override these values:
    -   `storage`: all.
    -   `interface`: all.
-   `required`: Whether or not user input is required. A value of false sets no default, and no user input will mean no parameters passed to the script. Defaults to `true`.
-   `results`: What results the script will return on completion. You can only define this parameter within the embedded YAML of the script. Results may be a list of strings or a dictionary of dictionaries.

Example script using default values:

``` python
#!/usr/bin/env python3

# --- Start MAAS 1.0 script metadata ---
# name: example
# parallel: instance
# parameters:
#   storage: {type: storage}
# --- End MAAS 1.0 script metadata ---

import argparse

parser = argparse.ArgumentParser(description='')
parser.add_argument(
    '--storage', dest='storage', required=True,
    help='path to storage device you want to test. e.g. /dev/sda')
args = parser.parse_args()

print("Testing: %s" % args.storage)
```

Example script using customised parameters:

``` bash
#!/bin/bash

# --- Start MAAS 1.0 script metadata ---
# name: example
# parallel: instance
# parameters:
#   storage:
#     type: storage
#     argument-format: '{model}' '{serial}'
# --- End MAAS 1.0 script metadata ---

echo "Model: $1"
echo "Serial: $2"
```

<h2 id="heading--environment-variables">Environment variables</h2>

The following environment variables are available when a script runs within the MAAS environment:

-   `OUTPUT_STDOUT_PATH`: The path to the log of STDOUT from the script.
-   `OUTPUT_STDERR_PATH`: The path to the log of STDERR from the script.
-   `OUTPUT_COMBINED_PATH`: The path to the log of the combined STDOUT and STDERR from the script.
-   `RESULT_PATH`: Path for the script to write a result YAML.
-   `DOWNLOAD_PATH`: The path where MAAS will download all files.
-   `RUNTIME`: The amount of time the script has to run in seconds.
-   `HAS_STARTED`: When 'True', MAAS has run the script once before but not to completion. Indicates the machine has rebooted.

<h2 id="heading--results">Results</h2>

A script can output its results to a YAML file, and those results will be associated with the hardware type defined within the script. MAAS provides the path for the results file in an environment variable, `RESULT_PATH`. Scripts should write YAML to this file before exiting.

If the hardware type is storage, for example, and the script accepts a storage type parameter, the result will be associated with a specific storage device.

The YAML file must represent a dictionary with the following fields:

-   `result`: The completion status of the script. This status can be `passed`, `failed`, `degraded`, or `skipped`. If no status is defined, an exit code of `0` indicates a pass while a non-zero value indicates a failure.
-   `results`: A dictionary of results. The key may map to a results key defined as embedded YAML within the script. The value of each result must be a string or a list of strings.

Optionally, a script may define what results to return in the YAML file in the [Metadata fields](#Metadata%20fields). The `results` field should contain a dictionary of dictionaries. The key for each dictionary is a name which is returned by the results YAML. Each dictionary may contain the following fields:

-   `title` - The title for the result, used in the UI.
-   `description` - The description of the field used as a tooltip in the UI.

Example degrade detection:

``` python
#!/usr/bin/env python3

# --- Start MAAS 1.0 script metadata ---
# name: example
# results:
#   memspeed:
#     title: Memory Speed
#     description: Bandwidth speed of memory while performing random read writes
# --- End MAAS 1.0 script metadata ---

import os
import yaml

memspeed = some_test()

print('Memspeed: %s' % memspeed)
results = {
    'results': {
        'memspeed': memspeed,
    }
}
if memspeed < 100:
    print('WARN: Memory test passed but performance is low!')
    results['status'] = 'degraded'

result_path = os.environ.get("RESULT_PATH")
if result_path is not None:
    with open(result_path, 'w') as results_file:
        yaml.safe_dump(results, results_file)
```

<h2 id="heading--script-examples">Script examples</h2>

<h3 id="heading--built-in-scripts">Built-in scripts</h3>

You can download the source for all commissioning and test scripts via the API with the following command:

``` bash
maas $PROFILE node-script download $SCRIPT_NAME
```

The source code to all built-in scripts is available on [launchpad](https://git.launchpad.net/maas/tree/src/metadataserver/builtin_scripts).

<h3 id="heading--commissioning-script-configure-hpa">Commissioning script: Configure HPA</h3>

Below is a sample script to configure an Intel C610/X99 HPA controller on an HP system. The script will only run on systems with an Intel C610/X99 controller identified by the PCI ID 8086:8d06.

Before the script runs, MAAS will download and install the [HP RESTful Interface Tool](https://downloads.linux.hpe.com/SDR/project/hprest/) package from HP. After the script completes, the built-in commissioning scripts will be re-run to capture the new configuration.

``` bash
#!/bin/bash -ex
# --- Start MAAS 1.0 script metadata ---
# name: hp_c610_x99_ahci
# title: Configure Intel C610/X99 controller on HP systems
# description: Configure Intel C610/X99 controller on HP systems to AHCI
# script_type: commissioning
# tags: configure_hpa
# packages:
#  url: http://downloads.linux.hpe.com/SDR/repo/hprest/pool/non-free/hprest-1.5-26_amd64.deb
# for_hardware: pci:8086:8d06
# recommission: True
# --- End MAAS 1.0 script metadata ---
output=$(sudo hprest get EmbeddedSata --selector HpBios.)
echo $output
if [ $(echo $output | grep -c 'EmbeddedSata=Raid') ]; then
    echo "Server is in Dynamic Smart Array RAID mode. Changing to SATA AHCI support mode."
    sudo hprest set EmbeddedSata=Ahci --selector HpBios. --commit
else:
    echo "No changes made to the system, Server is Already in AHCI Mode"
fi
```

<h3 id="heading--commissioning-script-update-firmware">Commissioning script: Update firmware</h3>

Below is a sample script to update the mainboard firmware on an ASUS P8P67 Pro using a vendor-provided tool. The tool will is automatically downloaded and extracted by MAAS. The script reboots the system to complete the update. The system will boot back into the MAAS ephemeral environment to finish commissioning and optionally testing.

[note]
Vendor tools which use UEFI boot capsules or need to store resource files on disk while rebooting are not currently supported.
[/note]

``` bash
#!/bin/bash -ex
# --- Start MAAS 1.0 script metadata ---
# name: update_asus_p8p67_firmware
# title: Firmware update for the ASUS P8P67 mainboard
# description: Firmware update for the ASUS P8P67 mainboard
# script_type: commissioning
# tags: update_firmware
# packages:
#  url: http://example.com/firmware.tar.gz
# for_hardware: mainboard_product:P8P67 PRO
# may_reboot: True
# --- End MAAS 1.0 script metadata ---
$DOWNLOAD_PATH/update_firmware
reboot
```

<h3 id="heading--hardware-test-script-cpu-stress-test">Hardware test script: CPU stress test</h3>

As a simple example, here's a functional Bash script replicating part of the stress-ng script bundled with MAAS:

``` bash
#!/bin/bash -e
# --- Start MAAS 1.0 script metadata ---
# name: stress-ng-cpu-test
# title: CPU validation
# description: Run stress-ng memory tests for 5 minutes.
# script_type: test
# hardware_type: cpu
# packages: {apt: stress-ng}
# tags: cpu
# timeout: 00:05:00
# --- End MAAS 1.0 script metadata ---

sudo -n stress-ng --matrix 0 --ignite-cpu --log-brief --metrics-brief --times \
    --tz --verify --timeout 2m
```

This Bash script contains comment-delineated metadata, which configures the script environment and installs any dependencies.  There is also a single line that runs **stress-ng** (a CPU stress-test utility) with various arguments.

<h3 id="heading--automatic-script-selection-by-hardware-type">Automatic script selection by hardware type</h3>

When selecting multiple machines in the [web UI](/t/node-overview/829), scripts which declare the `for_hardware` field will only run on machines with matching hardware. To automatically run a script when 'Update firmware' or 'Configure HBA' is selected, you must tag the script with 'update_firmware' or 'configure_hba'.

Similarly, scripts selected by tag on the [command line](/t/cli-testing-scripts/832) which specify the `for_hardware` field will only run on matching hardware.

<h2 id="heading--upload-procedure">Upload procedure</h2>

Scripts can be uploaded to MAAS using the web UI. Select the 'User scripts' tab of the 'Settings' page - the 'Commissioning scripts' section is near the top. Within the Commissioning scripts section, click the Upload script button followed by 'Choose file' to open a requester, locate the script, and select Upload to upload it to MAAS.

A status message of Commissioning script created will appear.  You'll then be able to select your script after selecting [Test hardware](/t/hardware-testing/826) from a machine's 'Take action' menu.

![select custom script](https://assets.ubuntu.com/v1/50e08fdf-nodes-hw-scripts__2.4_select.png)

[note]
MAAS executes scripts in lexicographical order. This order allows you to control when your scripts execute, and whether they run before or after the standard MAAS scripts.
[/note]

<h2 id="heading--debugging">Debugging</h2>

Clicking on the title of a completed or failed script will reveal the output from that specific script.

![failed script output](https://assets.ubuntu.com/v1/855015e5-nodes-hw-scripts__2.2_fail.png)

If you need further details, especially when writing and running your own scripts, you can connect to a machine and examine its logs and environment.

To do this, enable Allow SSH access and prevent the machine from powering off when selecting 'Test hardware' from the machine 'Take action' menu.

![enable SSH within Test Hardware](https://assets.ubuntu.com/v1/da793c67-nodes-hw-scripts__2.4_ssh.png)

Because scripts operate within an ephemeral version of Ubuntu, enabling this option stops the machine from shutting down, allowing you to connect and probe a script's status.

As long as you've added your [SSH key](/t/user-accounts/790#heading--ssh-keys) to MAAS, you can connect with SSH to the machine's IP with a username of `ubuntu`. Type `sudo -i` to get root access.

<h3 id="heading--access-individual-scripts-and-log-files">Access individual scripts and log files</h3>

<h4 id="heading--commissioning-and-testing-script-files">Commissioning and testing script files</h4>

-   `/tmp/user_data.sh.*/scripts/commissioning/`: Commissioning scripts
-   `/tmp/user_data.sh.*/scripts/testing/`: Hardware testing scripts

<h4 id="heading--log-files">Log files</h4>

-   `/tmp/user_data.sh*/out/`
-   `/var/log/cloud-init-output.log`
-   `/var/log/cloud-init.log`

<h3 id="heading--run-all-scripts-manually">Run all scripts manually</h3>

You can also run all commissioning and hardware-testing scripts on a machine for debugging.

``` bash
/tmp/user_data.sh.*/bin/maas-run-remote-scripts \
    [--no-download] \
    [--no-send] \
    /tmp/user_data.sh.*
```

Where:

-   `--no-download`: Optional. Do not download the scripts from MAAS again.
-   `--no-send`: Optional. Do not send the results to MAAS.

For example, to run all the scripts again without downloading again from MAAS:

``` bash
/tmp/user_data.sh.*/bin/maas-run-remote-scripts --no-download /tmp/user_data.sh.*
```

Here, all the scripts are run again after downloading from MAAS, but no output data is sent to MAAS:

``` bash
/tmp/user_data.sh.*/bin/maas-run-remote-scripts --no-send /tmp/user_data.sh.*
```

<h2 id="heading--command-line-access">Command line access</h2>

For information about managing scripts, applying tags to scripts and seeing script results using the CLI, please see [CLI Testing Scripts](/t/cli-testing-scripts/832).

<!-- LINKS -->
<!-- IMAGES -->