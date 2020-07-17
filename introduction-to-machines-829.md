Machines are the heart of MAAS. They are the backbone of your data centre application(s), providing the functions that are relevant to your customers. MAAS manages their transit through a life-cycle, from adding and enlistment, through commissioning, allocation, and deployment, finally being released back to the pool, or retired altogether.  You can move them around, create redundant versions (even in other geographies you can access), and basically rearrange them to the extent that your network allows.

#### Quick questions you may have:

* [How are the machine states and actions related?](/t/introduction-to-machines/829#heading--machine-life-cycle)
* [How can I view the machine list?](/t/introduction-to-machines/829#heading--machine-list)
* [How can I view a machine summary?](/t/introduction-to-machines/829#heading--machine-summary) 
* [How can I view machine details?](/t/introduction-to-machines/829#heading--node-details)
* [Where can I find network info for a machine?](/t/introduction-to-machines/829#heading--machine-interfaces-h3)
* [Where can I find storage info for a machine?](/t/introduction-to-machines/829#heading--machine-storage-h3)
* [Where can I find the commissioning log for a machine?](/t/introduction-to-machines/829#heading--commissioning-log-h3)
* [Where can I find machine hardware & test logs?](/t/introduction-to-machines/829w#heading--hardware-tests-h3)
* [Where can I find raw log output for a machine?](/t/introduction-to-machines/829w#heading--raw-log-output-h3)
* [Where can I find a machine's event log?](/t/introdunction-to-machines/829#heading--event-logs-h3)
* [Where can I find machine configuration info?](/t/introdunction-to-machines/829#heading--machine-config-h3)

For example, in the illustration below, you see a typical small hospital data centre, including servers ready and allocated for functions like Pharmacy, Orders, Charts, and so on:

![machine-list-general|690x416](https://discourse.maas.io/uploads/default/optimized/1X/30df04b0bcec5fcf6538590ed795cb0514a64675_2_690x416.jpeg) 

These example machines would typically be duplicated in several different geographies, with a quick way to switch to a redundant node, should anything go wrong (e.g., high availability).  We used the word node there because, In the network language of MAAS, machines are one of several different types of nodes.  A node is simply a network-connected object or, more specifically, an object that can independently communicate on a network. MAAS nodes include controllers, network devices, and of course, machines.   

Looking back at the example above, you can see that there are several columns in the machine list:

![machine-list-columns|690x105](https://discourse.maas.io/uploads/default/optimized/1X/e6339dd1439b3b54be7a75f7239d1981754a07f6_2_690x105.jpeg) 

The columns list the following details for each machine:

-   **FQDN | MAC**: The fully qualified domain name or the MAC address of the machine.
-   **Power**: 'On', 'Off' or 'Error' to highlight an error state.
-   **Status**: The current status of the machine, such as 'Ready', 'Commissioning' or 'Failed testing'.
-   **Owner**: The MAAS account responsible for the machine.
-   **Cores**: The number of CPU cores detected on the machine.
-   **RAM**: The amount of RAM, in GiB, discovered on the machine.
-   **Disks**: The number of drives detected on the machine.
-   **Storage**: The amount of storage, in GB, identified on the machine.

<h2 id="heading--machine-life-cycle">Machine life-cycle</h2>

One of the most important things to understand about machines is their life-cycle.  Machines can be disovered or added, commissioned by MAAS, acquired, deployed, released, marked broken, tested, put into rescue mode, and deleted.  In addition, pools, zones, and tags can be set for machines.

All of these states and actions represent the possible life-cycle of a machine.  This life-cycle isn't strict or linear -- it depends on how you use a machine -- but it's useful to give a general overview of how machines tend to change states.  In the discussion that follows, states and actions are shown in **bold** type.

* Machines start as servers in your environment, attached to a network or subnet MAAS can manage.

* If machines are configured to netboot, MAAS can **discover** them and present them to you for possible commissioning, changing their state to **New**.

* When you select a machine that is marked **New**, you can choose to **commission** it.  If you add a machine manually, it is automatically **commissioned**.

* Machines that have successfully commissioned can be **acquired** and **deployed**.  Machines that don't successfully commission can be **marked broken** (and later recovered when the issues are resolved).

* Resolving problems with machines usually involve **testing** the machine.

* Once you've deployed a machine, and you're done with it, you can **release** it.

* You can place a machine in **rescue mode**, which allows you to SSH to a machine to make configuration changes or do other maintenance. Once you're done, you can **exit rescue mode***.

* Any time a machine is on, you have the option to select it and **power off** that machine.

* You can **abort** any operation that's in progress.

* You also have the option to set tags, availiabity zone, or resource pool at various stages along the way.

Since these actions are not necessarily sequential, and the available actions change as the machine state changes, it's not very useful to make a state diagram or flowchart.  Instead, consider the following table:

| Action/State | New | Ready | Acquired | Deployed | Locked | Rescue | Broken |
|--------------|:---:|:-----:|:--------:|:--------:|:------:|:------:|:------:|
| Commission   | X   | X     |          |          |        |        |   X    |
| Acquire      |     | X     |          |          |        |        |        |
| Deploy       |     | X     |   X      |          |        |        |        |
| Release      |     |       |   X      |    X     |        |        |        |
| Power on     |     |       |          |    X     |        |        |   X    |
| Power off    |     |       |          |          |        |        |        |
| Test         | X   | X     |   X      |    X     |        |        |   X    |
| Rescue mode  | X   | X     |   X      |    X     |        |        |   X    |
| Exit rescue  |     |       |          |          |        |   X    |        |
| Mark broken  |     |       |   X      |    X     |        |        |        |
| Mark fixed   |     |       |          |          |        |        |   X    |
| Lock         |     |       |          |    X     |        |        |        |
| Unlock       |     |       |          |          |   X    |        |        |
| Tag          | X   | X     |   X      |    X     |        |   X    |   X    |
| Set zone     | X   | X     |   X      |    X     |        |   X    |   X    |
| Set...pool   | X   | X     |   X      |    X     |        |   X    |   X    |
| Delete       | X   | X     |   X      |    X     |        |   X    |   X    |

When a machine is in the state listed in a column, it is possible to take the row actions marked with an "X."  You access these actions from the "Take action" menu in the upper right corner of the machine listing.  Note that some actions, such as "Mark broken" or "Lock," may be hidden when they are not available.

<h2 id="heading--machine-list">View the machine list</h2>

Incidentally, you can get to this list of machines from the choice "Machines" on the top menu of the MAAS web UI.  This action will display a table like the one above, listing all the machines that are currently visible to your MAAS installation.  During commissioning and deployment, MAAS updates the table to reflect the changing state of each machine. These values are augmented with green, amber and red icons to represent successful, in-progress and failed transitions, respectively. The MAAS web UI employs similar icons and colours throughout the interface to reflect a machine's status. 

![machine-list-christmas-tree|663x500](https://discourse.maas.io/uploads/default/optimized/1X/19e038dbc6e669bfffc0ea5a9946432a75142bfb_2_663x500.jpeg) 

Rolling the cursor over status icons often reveals more details. For example, a failed hardware test script will place a warning icon alongside the hardware type tested by the script. Rolling the cursor over this will reveal which test failed.  Likewise, you can find some immediate options by rolling over the column data items in the machines table.

![rollover-icons|690x222](https://discourse.maas.io/uploads/default/optimized/1X/8f78a8877a029e7a44bcd4cf3d138499637fe790_2_690x222.jpeg) 

The 'Add hardware' drop-down menu is used to add either new machines or a new chassis. This menu changes context when one or more machines are selected from the table, using either the individual checkboxes in the first column or the column title checkbox to select all.

![machine-add-hardware-menu|690x235](https://discourse.maas.io/uploads/default/optimized/1X/9a0747649e6aff999d3c04335eb752accedaf3de_2_690x235.jpeg) 

With one or more machines selected, the 'Add hardware' drop-down menu moves to the left, and is joined by the 'Take action' menu.  This menu provides access to the various [machine actions](/t/concepts-and-terms/785#node-actions) that can be applied to the selected machine(s):

![machine-take-action-menu-dropped|690x364](https://discourse.maas.io/uploads/default/optimized/1X/e03d5ac8de9ea4f4827ed057bb2dd83e241aac3b_2_690x364.jpeg) 

[note]
The 'Filter by' section limits the machines listed in the table to selected keywords and machine attributes.
[/note]

<h2 id="heading--node-details">View machine details</h2>

Click a machine's FQDN or MAC address to open a detailed view of a machine's status and configuration.

![machine-details|690x439](https://discourse.maas.io/uploads/default/optimized/1X/c9684bf883d01d3fe610ec27e95618075c44b324_2_690x439.jpeg)

The default view is 'Machine summary', presented as a series of cards detailing the CPU, memory, storage and tag characteristics of the machine, as well as an overview of its current status. When relevant, 'Edit' links take you directly to the settings pane for the configuration referenced within the card.  The machine menu bar within the web UI also includes links to logs, events, and configuration options:
 ![machine-event-output|690x370](https://discourse.maas.io/uploads/default/optimized/1X/2efac92fca2c90f53ac86bd98485d8e98a1f91d4_2_690x370.jpeg) 

The menu includes links to a number of additional forms and controls, as described in the following sections.

<h3 id="heading--machine-summary-h3">View a machine summary</h3>

As shown above, the Machine summary presents an overview of CPU, memory, storage, tags, and general settings:

![machine-details|690x439](https://discourse.maas.io/uploads/default/optimized/1X/c9684bf883d01d3fe610ec27e95618075c44b324_2_690x439.jpeg)

The first card presents some basics of the machine resources and configuration:

![machine-summary-overview-card|690x119](https://discourse.maas.io/uploads/default/optimized/1X/3e50fb21f4985db0a85519e2e933e24658770b9e_2_690x119.jpeg) 

Here are some details on what this card presents, with details on in-card links described in following sections:
 - **OVERVIEW** the machine status (in this case "Deployed"), and lists OS version information.  
 - **CPU** shows the specifics of the CPU(s), including a link to test the processor(s).
 - **MEMORY** gives the total available RAM for this machine, along with a test link.
 - **STORAGE** presents the total amount of storage available and the number of disks that provide that storage.  There are two links here: one gives the storage layout (with the opportunity to change it for devices that are in 'Ready' or 'Allocated' states.
 - **Owner** identifies the owner of the machine.
 - **Domain** indicates the domain in which the machine exists.
 - **Zone** shows the AZ in which this machine resides, along with a link to edit the machine configuration (to change the AZ, if desired).
 - **Resource pool** shows the pool to which this machine has been assigned, and an edit link.
 - **Power type** gives the current power type, which links to the relevant edit form.
 - **Tags** presents the list of tags associated with this machine, editable via the link.

Note that clicking any of the links in this card will either present a pop-up form or take you to another item in the machine menu -- so using the browser "back" button will take you completely away from this machine's page.  For example, you can choose the "Test CPU" option, which brings up this overlay:

![cpu-test|690x215](https://discourse.maas.io/uploads/default/optimized/1X/4fe98db93dd34f3b167b56286b06ec6d244d5848_2_690x215.jpeg) 

From this screen, you can choose test scripts and run the tests (in the background) as the interface returns to the Machine summary.  A linked note in the CPU block lets you know that the tests are in progress:

![cpu-testing-in-progress|690x231](https://discourse.maas.io/uploads/default/original/1X/3d6996b32a5193dab76b112c864a216c845aa985.jpeg) 

And you can watch the results under the "Tests" option in the Machine menu:

![cpu-tests-log|690x185](https://discourse.maas.io/uploads/default/optimized/1X/89d287347928d6adb039ca582d4a94c3b54588e1_2_690x185.jpeg) 

The rest of the cards on the Machine summary are either self-explanatory, or they're covered in the sections below.  The main point is this: You can see that nearly everything about machines takes place within the main menu's "Machines" option.  Incidentally, you can learn more about testing by visiting the [Hardware testing](https://maas.io/docs/hardware-testing) page.

<h3 id="heading--machine-interfaces-h3">Find network info for a machine</h3>

The Network "tab" provides you with a way to view/edit the network and interface configuration for a machine: 

![machine-network-tab|690x206](https://discourse.maas.io/uploads/default/optimized/1X/7a7b22ee7202b50df09c7bef598250db0eb1cc15_2_690x206.jpeg) 

In the case of this deployed machine, there are not many editing options.  If the machine is in a 'Ready' state, though, altering the network configuration is possible:

![machine-network-tab-undeployed-machine|690x176](https://discourse.maas.io/uploads/default/optimized/1X/01d53759105647d71786154e99ff210f8c71d2e7_2_690x176.jpeg) 

Options on this tab are described in the introduction to [Networking](https://maas.io/docs/networking) article in this documentation set.

<h3 id="heading--machine-storage-h3">Find storage info for a machine</h3>

The Storage tab on the machine list brings up a form that allows you to view/edit the file system, partitioning and storage parameters for the selected machine:

![machine-storage-tab|690x431](https://discourse.maas.io/uploads/default/optimized/1X/9dc30aedf5ed173bb7b474910fa9939f7f066c95_2_690x431.jpeg) 

This tab describes the filesystem(s) in use, as well as the available and used partitions for this machine.  See the article [Storage](https://maas.io/docs/storage) for a detailed discussion on how to use this screen, as well as many other considerations for machine storage configurations.

<h3 id="heading--commissioning-log-h3">Find the commissioning log for you</h3>

The "Commissioning" tab brings up a summary log of commissioning events:

![comm-logging-summary|690x279](https://discourse.maas.io/uploads/default/optimized/1X/b3d6248013fb5186d3ea61931816fe688b94a6a2_2_690x279.jpeg) 

Clicking on any of the "View log" links will take you to specific, detailed logs for that particular event or milestone:

![comm-logs-details|690x379](https://discourse.maas.io/uploads/default/optimized/1X/13fce404b9bb304e08a7c5de5a395c514e98b9b0_2_690x379.jpeg) 

These logs present an extremely detailed, timestamped record of completion and status items from the commissioning process.  See the article on [Logging](https://maas.io/docs/logging) for more details on how to read and interpret these logs.

<h3 id="heading--hardware-tests-h3">Find machine hardware & test logs</h3>

This tab presents a summary of tests run against this particular machine:  

![machine-menu-test-summary|690x189](https://discourse.maas.io/uploads/default/optimized/1X/b6a6ff3c8fae4a638c18a18bf65c4aa51f29e984_2_690x189.jpeg) 

You can view the summary report, or click on a "View log" link to get details on any particular tests:

![machine-test-tab-details|690x431](https://discourse.maas.io/uploads/default/optimized/1X/8113eba28a3f08279ff2b5f7593e30db05d00824_2_690x431.jpeg) 

The format of these screens is very similar to the Configuration logs shown above.  For more information, please see the article on [Hardware testing](https://maas.io/docs/hardware-testing).

<h3 id="heading--raw-log-output-h3">Find raw log output for a machine</h3>

The "Logs" tab shows raw log output, switchable between YAML and XML formats:

![machine-logs-tab|690x439](https://discourse.maas.io/uploads/default/optimized/1X/02b4bc35c8985fe0b954d8deb0afba18866cfe66_2_690x439.jpeg) 

Help interpreting these logs can be found under the [Logging](https://maas.io/docs/logging) section of this documentation.

<h3 id="heading--event-logs-h3">Find a machine's event logs</h3>

The "Event" tab displays a list ot timestamped status updates for events and actions performed on the machine:

![machine-list-event-tab-log|690x305](https://discourse.maas.io/uploads/default/optimized/1X/114ada7188f8ebf12883a54c0976ad8abda1d211_2_690x305.jpeg) 

There is a button that allows you to see the next 10 events, and a link to show the entire history.  Detailed discussion of this event log can be found under the [Logging](https://maas.io/docs/logging) section of this documentation.

<h3 id="heading--machine-config-h3">Find machine configuration info</h3>

The final tab from the Machine menu allows you to update machine and power configuration options: 

![machine-config-tab-failed-testing|690x412](https://discourse.maas.io/uploads/default/optimized/1X/13bb1352103c759365fc6b923672f021982a10c3_2_690x412.jpeg") 

There are two sections to this tab.  The "Machine configuration" section offers some general parameters, mostly related to how this machine is grouped and categorised.  More information on these options are found in the relevant sections of the documentation (e.g., tags, resource pools, and so forth). 

The "Power configuration" supplies the parameters necessary for MAAS to access the machine to PXE-boot it. Note that this machine failed testing.  Editing the "Power configuration" section gives us a clue as to what might be wrong:
 ![power-config-failed-login|690x192](https://discourse.maas.io/uploads/default/optimized/1X/8f4c4ae8ec07f42551bad654375f513debc53ef1_2_690x192.jpeg) 

After entering the correct password and recycling things, the problem goes away:

![machine-config-no-error|690x388](https://discourse.maas.io/uploads/default/optimized/1X/635e27e87bcac0c3cb3ce3085bd2fa55f60ff137_2_690x388.jpeg) 

More information on Power configuration will be found in the [Power management](https://maas.io/docs/bmc-power-types) section of this documentation.

<h2>Summary</h2>

This article has offered you a cursory glimpse into machines and how they are configured and managed in MAAS.  Read on through this section of the documentation to learn more.

<!-- LINKS -->