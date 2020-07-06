To manage a machine, MAAS must be able to power cycle it, usually through the machine's [BMC](https://en.wikipedia.org/wiki/Intelligent_Platform_Management_Interface#Baseboard_management_controller) card.  Until you configure the power type, a newly-added machine can't be enlisted and used by MAAS.

#### Quick questions you may have:

* [How do I configure a machine's power type?](/t/power-management/830#heading--config-power-type)
* [Can you give me an example of the virsh power type?](/t/power-management/830#heading--example-virsh-kvm-power-type)
* [Which BMC drivers are supported?](/t/power-management/830#heading--bmc-driver-support)

<h2 id="heading--config-power-type">Configure a machine's power type</h2>

To configure a machine's power type, click on the machine from the 'Machines' page of the web UI, then select its 'Configuration' tab. Scroll down until you find the Power configuration. If the power type is undefined, the following will be displayed:

![power types undefined](https://assets.ubuntu.com/v1/4fae5977-nodes-power-types__2.4_undefined.png)

Choose a type in the dropdown menu that corresponds to the machine's underlying machine's BMC card.

![power types selection](https://assets.ubuntu.com/v1/b53c6613-nodes-power-types__2.4_selection.png)

Fill in the resulting form; the information required will depends on the power type.

Click 'Save changes' to finish. Once that's done, MAAS performs a power check on the machine. A successful power check is a good indication that MAAS can properly communicate with the machine, that is, it should quickly result in a power status of "Power off". A failed attempt will show:

![power types power error](https://assets.ubuntu.com/v1/3bd5e93b-nodes-power-types__2.4_power-error.png)

If you see this error, double-check your entered values by editing the power type, or  consider another power type altogether.

Another possible cause for this error may be the networking: traffic may be getting filtered between the rack controller and the BMC card.

<h2 id="heading--example-virsh-kvm-power-type">An example: the Virsh power type</h2>

Consider a machine backed by VM. Below, a 'Power type' of `Virsh` has been selected, and the 'Power address' of `qemu+ssh://ubuntu@192.168.1.2/system` has been entered (replace values as appropriate).  The value of 'Power ID' is the VM domain (guest) name, here `node2`.

![power types example: virsh](https://assets.ubuntu.com/v1/c75e00a8-nodes-power-types__2.4_example-virsh.png)

[note]
The machine's hostname -- according to MAAS -- is a randomly chosen string (here `dear.ant`). You should change this hostname to something descriptive, that helps you remember why this machine is in your MAAS network.
[/note]

<h2 id="heading--bmc-driver-support">Which BMC drivers are supported</h2>

MAAS supports many types of BMC hardware, though not all the drivers have the same capabilities. See the below table for a feature comparison of the BMC drivers currently supported by MAAS.

<details><summary>Tell me about BMC</summary>

BMC, or "Baseboard Management Controller," is an extra microcontroller on the motherboard of a server which forms the interface between system-management software and the device's hardware.  The BMC can collect data from attached sensors, alert administrators to issues, and respond to remote-control commands to control system operation or power state, independent of the system's CPU.

In the context of MAAS, the BMC is generally controlled by SNMP commands.  Any given BMC will function in the context of one or more "power types," which are physical interfaces that permit use of the IPMI ("Intelligent Platform Management Interface") protocol.  Each power type has a different set of expected parameters required to access and command the BMC.

</details>

<table>
<colgroup>
<col width="35%" />
<col width="12%" />
<col width="10%" />
<col width="14%" />
<col width="15%" />
<col width="11%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Power Driver (<em>X=supported</em>)</th>
<th>PXE Next Boot</th>
<th>Power Querying</th>
<th>Chassis/Pod Configuration</th>
<th>Enhanced UI Error Reporting</th>
<th>BMC Enlistment</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">American Power Conversion (APC) - PDU</td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td align="left">Cisco UCS Manager</td>
<td>X</td>
<td>X</td>
<td>X</td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td align="left">Digital Loggers, Inc. - PDU</td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td align="left">Facebook's Wedge <code>*</code></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td align="left">HP Moonshot - iLO Chassis Manager</td>
<td>X</td>
<td>X</td>
<td>X</td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td align="left">HP Moonshot - iLO4 (IPMI)</td>
<td>X</td>
<td>X</td>
<td></td>
<td></td>
<td>X</td>
</tr>
<tr class="odd">
<td align="left">IBM Hardware Management Console (HMC)</td>
<td>X</td>
<td>X</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td align="left">IPMI</td>
<td>X</td>
<td>X</td>
<td></td>
<td>X</td>
<td>X</td>
</tr>
<tr class="odd">
<td align="left">Intel AMT</td>
<td>X</td>
<td>X</td>
<td></td>
<td>X</td>
<td></td>
</tr>
<tr class="even">
<td align="left">Manual</td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td align="left">Microsoft OCS - Chassis Manager</td>
<td>X</td>
<td>X</td>
<td>X</td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td align="left">OpenStack Nova</td>
<td></td>
<td>X</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td align="left">Rack Scale Design</td>
<td>X</td>
<td>X</td>
<td>X</td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td align="left">SeaMicro 15000</td>
<td>X</td>
<td>X</td>
<td>X</td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td align="left">Sentry Switch CDU - PDU</td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td align="left">VMWare</td>
<td>X</td>
<td>X</td>
<td>X</td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td align="left">Virsh (virtual systems)</td>
<td>X</td>
<td>X</td>
<td>X</td>
<td></td>
<td></td>
</tr>
</tbody>
</table>

`*` The 'Facebook's Wedge' OpenBMC power driver is considered experimental at this time.