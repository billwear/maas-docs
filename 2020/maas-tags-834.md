Tags are a convenient way to assign descriptive words to machines, interfaces, and storage devices. Using MAAS tags, you can easily select machines that meet certain criteria, helping you to remember that you've defined or reserved certain machines for certain functions. 

[note]
 Tag names can include any combination of letters, numbers, dashes (-) and underscores (_), and be a maximum of 256 characters in length.
[/note]

#### Quick questions you may have:

* [How are tags defined?](/t/maas-tags/834#heading--tag-definitions)
* [How can I see and filter the tag list?](/t/maas-tags/834#heading--tag-listing-and-tags-as-search-filters)
* [How do I see and change which tags are assigned?](/t/maas-tags/834#heading--tag-assignment)
* [How do I manage tags?](/t/maas-tags/834#heading--tag-management)

MAAS supports binding an XPath expressions to a tag using *tag definitions* (see below). This makes auto-assigning tags to matching hardware possible. For instance, you could tag machines that possess fast GPUs and then deploy software that used GPU-accelerated CUDA or OpenCL libraries.

Because [Juju](https://jujucharms.com/docs/stable/about-juju.html) is the recommended way to deploy services on machines managed by MAAS (see [below](#heading--tag-management)), it supports MAAS tags for application deployments. By specifying MAAS tags as Juju "constraints", services can be deployed to machines that have particular user-defined characteristics.

<h2 id="heading--tag-definitions">Tag definitions</h2>

A *tag definition* is the criteria by which machines are auto-labelled by the corresponding tag. During machine enlistment, MAAS collects hardware information (using the [lshw](http://ezix.org/project/wiki/HardwareLiSter) utility). The definition used in creating a tag is then constructed using an *XPath expression* based on that information. See [w3schools documentation](https://www.w3schools.com/xml/xpath_intro.asp) for details on XPath.

The collected data for each machine, viewable (in both XML and YAML) in the web UI, is inspected by you for the desired property. Building on the example alluded to above, a property can be a GPU with a clock speed greater than 1GHz. In this case, the following excerpt from a machine's data (in XML format) is pertinent:

``` nohighlight
      <lshw:node id="display" class="display" handle="PCI:0000:00:02.0">
       <lshw:description>VGA compatible controller</lshw:description>
       <lshw:product>GD 5446</lshw:product>
       <lshw:vendor>Cirrus Logic</lshw:vendor>
       <lshw:physid>2</lshw:physid>
       <lshw:businfo>pci@0000:00:02.0</lshw:businfo>
       <lshw:version>00</lshw:version>
       <lshw:width units="bits">32</lshw:width>
       <lshw:clock units="Hz">33000000</lshw:clock>
       <lshw:configuration>
        <lshw:setting id="latency" value="0"/>
       </lshw:configuration>
       <lshw:capabilities>
        <lshw:capability id="vga_controller"/>
       </lshw:capabilities>
       <lshw:resources>
        <lshw:resource type="memory" value="fc000000-fdffffff"/>
        <lshw:resource type="memory" value="febd0000-febd0fff"/>
        <lshw:resource type="memory" value="febc0000-febcffff"/>
       </lshw:resources>
      </lshw:node>
```

MAAS machines will be selected based on these XPath *predicates*:

-   *element* of 'node'
-   with an *attribute* of 'id'
-   whose *value* is 'display'
-   and has a *child element* of 'clock units="Hz"'

After adding the speed criteria via an XPath *operator* we end up with this as our tag definition:

``` nohighlight
//node[@id="display"]/'clock units="Hz"' > 1000000000
```

<h2 id="heading--tag-listing-and-tags-as-search-filters">Tag listing and tags as search filters</h2>

To list all tags, visit the 'Machines' tab and expand the 'Tags' subsection in the left pane.

In this view, you can use tags as machine search filters. Select one or several tags. The machines that satisfy all selected tags will display on the right pane. Notice there is a search field at the top of the right pane. You can type a search expression into this field.

Below, tag 'virtual' has been selected (with the mouse), and the search field automatically reflects this. Five machines satisfy this search filter.

![tags: search](https://assets.ubuntu.com/v1/69aa9997-nodes-tags__2.6-tags-filter.png)

Remove a tag from the search filter by either hitting the 'x' character alongside a tag or editing the search expression.

<h2 id="heading--tag-assignment">Tag assignment</h2>

To view a machine's currently assigned tags, stay on the 'Machines' page and click on the machine in question. MAAS will display currently-assigned tags in the *Tags* pane of the 'Machine summary'.

Select the 'Configuration' tab and then the 'Edit' button alongside 'Machine configuration' to edit tags:

-   To unassign a tag hit the 'x' character alongside a tag.
-   To create a basic tag, type the name of the new tag in the 'Add a tag' field and hit Enter. The tag will be created and automatically assigned to the machine. Repeat if desired.
-   To assign an existing tag, type at least three characters to trigger a real-time search. Any resulting tags will show up in a drop-down menu. Select as desired.

![tags: add & remove](https://assets.ubuntu.com/v1/250050ee-nodes-tags__2.6-tags-add-remove.png)

You can apply changes by pressing the 'Save changes' button.

[note]
New tags become available as a filter in the 'Machines' page in the web UI immediately after you add them.
[/note]

<h3 id="heading--tags-for-network-interfaces">Tags for network interfaces</h3>

It's also possible to assign tags to specific network interfaces. You can use these tags when searching for machines within the web UI and when allocating machines from the API.

Network interface tags can only be assigned when a machine is in either a 'Ready' or a 'Broken' state.

With the machine selected from the 'Machines' page, on the 'Interfaces' tab use the 'Edit Physical' button from the 'Actions' menu icon to the right of an interface:

![tags: net interface](https://assets.ubuntu.com/v1/dd9cf996-nodes-tags__2.6-tag-net-interfaces.png)

To add a tag, type its name into the 'Tags' field and press Enter. Repeat as desired. Use the small 'x' next to a tag to unassign the tag.

You can apply changes by pressing the 'Save' button.

<h3 id="heading--tags-for-storage-configuration">Tags for storage configuration</h3>

Alongside tags for an entire machine and network interfaces, you can also define tags for storage devices. You can use these tags like any other, when searching for machines within the web UI, where they appear beneath the 'Storage tag' filter, and when allocating machines from the API.

To add a tag to a storage device, click on the machine with the storage you wish to tag on the 'Machines' page of the web UI and open the 'Storage' tab. Use the menu in the 'Actions' column beneath 'Available disks and partitions' to select 'Edit disk' for your chosen device:

![tags: storage](https://assets.ubuntu.com/v1/43dd9f9d-nodes-tags__2.6-tag-storage.png)

To add a tag, type its name into the 'Tags' field and press Enter. Repeat as desired. Use the small 'x' next to a tag to unassign the tag.

You can apply changes by pressing the 'Save' button.

<h2 id="heading--tag-management">Tag management</h2>

At this time, other than simple tag assignments as demonstrated above, the best way to manage MAAS tags is by using the CLI (see [CLI Tag management](/t/cli-tag-management/801)). Juju integration is also covered there.

![tags: multiple](https://assets.ubuntu.com/v1/fff81a99-nodes-tags__2.6-tag-multiple.png)

<!-- LINKS -->