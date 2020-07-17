Once you've successfully installed MAAS (regardless of method), you can now login here:

http://${API_HOST}:5240/MAAS

where $API_HOST is the hostname or IP address of the region API server, which was set during installation.  You will see a screen like this:</p>

![login-screen|690x327](https://discourse.maas.io/uploads/default/original/1X/efd8e3f150dfec28114c452c12e24e320848e075.jpeg) 

Log in at the prompts, with the login information you created when initialising MAAS.

<h2 id="heading--configuration">Configuration</h2>

After a fresh MAAS installation, the web UI presents a couple of welcome  screens.  From these screens, you can set many system-wide options, including connectivity, image downloads, and authentication keys.

![config-journey-1|690x460](https://discourse.maas.io/uploads/default/original/1X/337aa15e178b14b0ba9a0646953268bf7adac0bb.jpeg)  

Your main concerns for this experiment are the DNS forwarder, the Ubuntu image import section, and the SSH public key, though you might want to set the region name to something memorable, since this text will appear at the bottom of every MAAS screen in this install domain. Set the DNS forwarder to something obvious, e.g., `8.8.8.8`, Google’s DNS server.  Set this to your own internal DNS server if you know the IP address.

![config-journey-2|690x344](https://discourse.maas.io/uploads/default/original/1X/f9751eb857dcd0c124783edeb1aaf87b8b538127.jpeg)  

Select an Ubuntu image to import, noting that you may be required to select at least one LTS version, depending upon the version of MAAS that snap installed.  In this example, we've already chosen an image, and downloading is partially complete.

![image-download-1|690x416](https://discourse.maas.io/uploads/default/original/1X/6445cde5ffc1e237a1e6d85d280f451bc0b2ab92.jpeg)  

When you click on “Continue,” the screen will shift to a screen labelled, “SSH keys for admin:”  

![config-journey-ssh-keys|690x178](https://discourse.maas.io/uploads/default/optimized/1X/dbdcdce7c8f3b7181f894bdfe987758e0c8635fc_2_690x178.jpeg) 

In the source dropdown, select “Launchpad,” “Github,” or “Upload.”  If you choose one of the first two, you will need to enter your username for that service.  For example, if you want to upload your SSH public key from Launchpad, you would enter:

    lp:<username>


![config-ssh-select-key-source|690x190](https://discourse.maas.io/uploads/default/original/1X/0e4cbf7c8fae3f21664a4d5fe8d0f90785dd6859.jpeg) 

Likewise, if you want to upload your github public SSH key, you would enter:

    gh:<username>

If you want to use your existing public key from your home directory, you can select “Upload”and then copy your entire public key from <code>.ssh/id_rsa.pub</code> (or wherever you may have stored the key):

![copying-ssh-keys|690x250](https://discourse.maas.io/uploads/default/original/1X/a94f1f68db07dd9be9e8eaed50f22828c7bb51e0.jpeg) 

and paste it into the block labelled “Public key.”  Finally, press the “Import” button to import this key:

![pasting-ssh-keys|690x234](https://discourse.maas.io/uploads/default/optimized/1X/ab62bf22308343988016189fbbb851c44caf2e33_2_690x234.jpeg) 

With this complete, you’ll see that MAAS has been successfully set up. Click ‘Go to the Dashboard’ to proceed.

[note]
Note that you may have to wait a few moments for your selected images to sync locally.
[/note]

<h2 id="heading--networking">Networking</h2>

The Dashboard landing page lists non-registered devices that MAAS detected automatically on the network. This [network discovery](/t/network-discovery/758) process allows you to easily add or map devices already connected to your network – devices that you may not necessarily want to manage with MAAS.

![discovery|690x392](https://discourse.maas.io/uploads/default/optimized/1X/902f07b6e96d06dcd072501473ce85ff3d303610_2_690x392.jpeg)  

[note]
Network discovery can be disabled at any time from the button on the Dashboard view.  Also note that you can get back to the dashboard at any time by clicking the MAAS logo.

<h3 id="heading--spaces-fabrics-zones-and-subnets">Spaces, fabrics, zones and subnets</h3>

Networks in large data centres can be very complex. MAAS offers comprehensive control over networking so that you have the flexibility to reconfigure racks and deploy machines as you see fit. You can isolate machine deployment not only with DNS domains, but also via [subnets](/t/concepts-and-terms/785#heading--subnets), [spaces](/t/concepts-and-terms/785#heading--spaces), [zones](/t/concepts-and-terms/785#heading--zones), and [fabrics](/t/concepts-and-terms/785#heading--fabrics).  The links provide more details, but these are all basically collections:

* **subnet** has the traditional meaning: a range of IP addresses covering a subset of IP addresses.  Generally speaking , a subnet is a collection of IP addresses which includes at least two addresses.
* a **space** is a collection of subnets that you can create with MAAS, understanding that each subnet can belong to only one space.  Spaces allow multiple subnets to communicate without requiring a direct network path between them.
* a **zone** is also an ad-hoc collection, but one which groups individual nodes, rather than subnets.  MAAS allows you to create and edit zones at will.
* a **fabric** is essentially a collection of trunked switches, allowing you to access a group of VLANs.

Here is a diagram that helps to illustrate these concepts:

![fabrics-etc-example|690x442](https://discourse.maas.io/uploads/default/optimized/1X/dd60fdeba34d3cf33d4cf42db1f745ba95542b69_2_690x442.jpeg) 

Be aware that these network settings are spread across several web UI configuration pages. The Zones page, for example, enables you to see how many machines, devices and controllers are using a zone, and allows you to add and edit zones.  The Subnets page, shown below, provides access to fabric, VLAN, subnet and spaces configuration.

![subnet-list-full|690x297](https://discourse.maas.io/uploads/default/optimized/1X/89d90a15e70a57e6951ee62910b503895e08251e_2_690x297.jpeg) 

<h2 id="heading--deploy-hardware">Deployment</h2>

MAAS-managed machines are listed – and operated on – from the Machines page, making it one of the most important screens in the MAAS web UI:

![deploying-machines|690x394](https://discourse.maas.io/uploads/default/optimized/1X/58a37e0dc29bc233f771c33d07a0e03e8d55cb87_2_690x394.jpeg) 

If you are testing MAAS using virtual machines, the machines appear here as soon as they boot. New machines are added automatically when they first connect to your network. Alternatively, the ‘Add hardware’ menu lets you add machines manually, via their MAC address.

After you configure power and interfaces, MAAS must commission machines to retrieve CPU, memory and storage information. From this point, you can command MAAS to acquire, test, deploy and release these machines as you work with your MAAS cloud.

While you are testing MAAS, be sure to check out filters, which can narrow your view based on both [tags](/t/maas-tags/834) and hardware characteristics.  You can select and manage machines in either filtered or full views.

<h3 id="heading--images">Images</h3>

When it comes to running applications, MAAS can easily deploy any supported variant of Ubuntu, including LTS and non-LTR versions for x86, ARM, PPC and s390x systems. You can also deploy several other operating systems to your machines, including CentOS 7, CentOS 6, Windows, RHEL, and ESXi images, via <a href="https://www.ubuntu.com/support" rel="nofollow noopener">Ubuntu Advantage</a>.

![images-menu-screen|690x351](https://discourse.maas.io/uploads/default/optimized/1X/27c47222c1fc0e34ed70134a1007dde067d2de81_2_690x351.jpeg) 

<h3 id="heading--vm-hosts">VM hosts</h3>

[VM hosts]( /t/introduction-to-vm-hosting/1524) can give you greater control over your hardware.  A VM host is a collection of individual virtual machines.  You can use a VM host to compose machines into an abstraction of resources that functions like a physical machine – without building one! It’s easy to add a VM host: click the "Add KVM" button on the "KVM" page of the web UI, give the VM host a name, and select "Virsh" as the VM host type.  You will also need to enter the address for the [libvirt](https://ubuntu.com/server/docs/virtualization-libvirt) controlled pool below.

After you create a VM host, you compose hardware by selecting the VM host, and then selecting "Compose" from the Action menu. You can configure composed hardware as desired, including the number of cores, CPU speed, RAM and combined storage.  Then just click "Compose machine", and MAAS will combine resources to create a new, single entity that can be used just like any other machine.  VM hosts abstract multiple resources:

![pod1|690x398](https://discourse.maas.io/uploads/default/optimized/1X/c57d7cf802bfd3f968cc54a829cd1629c45e9f62_2_690x398.jpeg) 

into pools of composable hardware:

![compose-machine|690x422](https://discourse.maas.io/uploads/default/optimized/1X/763029b5678b6f88317359d28eac3003f7298f37_2_690x422.jpeg) 

There you have it: A quick tour of MAAS and its capabilities.  Read on through the documentation to learn more.