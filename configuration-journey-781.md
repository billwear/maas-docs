Once you've successfully installed MAAS (regardless of method), you can now login here:

http://${API_HOST}:5240/MAAS

where $API_HOST is the hostname or IP address of the region API server, which was set during installation.  You will see a screen like this:</p>

![login-screen|690x327](upload://ydMNEXxqpjRdKgVR1mZAVzwkNa5.jpeg) 

Log in at the prompts, with the login information you created when initialising MAAS.

<h2 id="heading--configuration">Configuration</h2>

After a fresh MAAS installation, the web UI presents a couple of welcome  screens.  From these screens, you can set many system-wide options, including connectivity, image downloads, and authentication keys.

![config-journey-1|690x460](upload://7lp5hQJ4HCdJTgiRXQP8wnX9IDh.jpeg)  

Your main concerns for this experiment are the DNS forwarder, the Ubuntu image import section, and the SSH public key, though you might want to set the region name to something memorable, since this text will appear at the bottom of every MAAS screen in this install domain. Set the DNS forwarder to something obvious, e.g., `8.8.8.8`, Google’s DNS server.  Set this to your own internal DNS server if you know the IP address.

![config-journey-2|690x344](upload://zANOKDNujqmvc5CuVv7J54yshmf.jpeg)  

Select an Ubuntu image to import, noting that you may be required to select at least one LTS version, depending upon the version of MAAS that snap installed.  In this example, we've already chosen an image, and downloading is partially complete.

![image-download-1|690x416](upload://ej3iJ4wobtanfLVRDWwzNXXgzei.jpeg)  

When you click on “Continue,” the screen will shift to a screen labelled, “SSH keys for admin:”  

![config-journey-ssh-keys|690x178](upload://vmZLhz8veCHo0mLugmdfqpZ1oVC.jpeg) 

In the source dropdown, select “Launchpad,” “Github,” or “Upload.”  If you choose one of the first two, you will need to enter your username for that service.  For example, if you want to upload your SSH public key from Launchpad, you would enter:

    lp:<username>


![config-ssh-select-key-source|690x190](upload://22v7dsNmDqnU582bGGUANsMmzMt.jpeg) 

Likewise, if you want to upload your github public SSH key, you would enter:

    gh:<username>

If you want to use your existing public key from your home directory, you can select “Upload”and then copy your entire public key from <code>.ssh/id_rsa.pub</code> (or wherever you may have stored the key):

![copying-ssh-keys|690x250](upload://o9Md9igpM0tG4uYJVbcWZtyWj6M.jpeg) 

and paste it into the block labelled “Public key.”  Finally, press the “Import” button to import this key:

![pasting-ssh-keys|690x234](upload://os9d5rLrJSfLgwwpKnx463UQM5Z.jpeg) 

With this complete, you’ll see that MAAS has been successfully set up. Click ‘Go to the Dashboard’ to proceed.

[note]
Note that you may have to wait a few moments for your selected images to sync locally.
[/note]

<h2 id="heading--networking">Networking</h2>

The Dashboard landing page lists non-registered devices that MAAS detected automatically on the network. This [network discovery](/t/network-discovery/758) process allows you to easily add or map devices already connected to your network – devices that you may not necessarily want to manage with MAAS.

![discovery|690x392](upload://kzvw4Fu7M65zHi4cjuKfKci3n8Y.jpeg)  

[note]
Network discovery can be disabled at any time from the button on the Dashboard view.  Also note that you can get back to the dashboard at any time by clicking the MAAS logo.

<h3 id="heading--spaces-fabrics-zones-and-subnets">Spaces, fabrics, zones and subnets</h3>

Networks in large data centres can be very complex. MAAS offers comprehensive control over networking so that you have the flexibility to reconfigure racks and deploy machines as you see fit. You can isolate machine deployment not only with DNS domains, but also via [subnets](/t/concepts-and-terms/785#heading--subnets), [spaces](/t/concepts-and-terms/785#heading--spaces), [zones](/t/concepts-and-terms/785#heading--zones), and [fabrics](/t/concepts-and-terms/785#heading--fabrics).  The links provide more details, but these are all basically collections:

* **subnet** has the traditional meaning: a range of IP addresses covering a subset of IP addresses.  Generally speaking , a subnet is a collection of IP addresses which includes at least two addresses.
* a **space** is a collection of subnets that you can create with MAAS, understanding that each subnet can belong to only one space.  Spaces allow multiple subnets to communicate without requiring a direct network path between them.
* a **zone** is also an ad-hoc collection, but one which groups individual nodes, rather than subnets.  MAAS allows you to create and edit zones at will.
* a **fabric** is essentially a collection of trunked switches, allowing you to access a group of VLANs.

Here is a diagram that helps to illustrate these concepts:

![fabrics-etc-example|690x442](upload://vApk9I4N4putFW6E85qvDrzkIWZ.jpeg) 

Be aware that these network settings are spread across several web UI configuration pages. The Zones page, for example, enables you to see how many machines, devices and controllers are using a zone, and allows you to add and edit zones.  The Subnets page, shown below, provides access to fabric, VLAN, subnet and spaces configuration.

![subnet-list-full|690x297](upload://jFspVCcfdtMwOAfXu9MMhW7qRzM.jpeg) 

<h2 id="heading--deploy-hardware">Deployment</h2>

MAAS-managed machines are listed – and operated on – from the Machines page, making it one of the most important screens in the MAAS web UI:

![deploying-machines|690x394](upload://cE8iushgIbD3yO6QQPPDZ8EDj0P.jpeg) 

If you are testing MAAS using virtual machines, the machines appear here as soon as they boot. New machines are added automatically when they first connect to your network. Alternatively, the ‘Add hardware’ menu lets you add machines manually, via their MAC address.

After you configure power and interfaces, MAAS must commission machines to retrieve CPU, memory and storage information. From this point, you can command MAAS to acquire, test, deploy and release these machines as you work with your MAAS cloud.

While you are testing MAAS, be sure to check out filters, which can narrow your view based on both [tags](/t/maas-tags/834) and hardware characteristics.  You can select and manage machines in either filtered or full views.

<h3 id="heading--images">Images</h3>

When it comes to running applications, MAAS can easily deploy any supported variant of Ubuntu, including LTS and non-LTR versions for x86, ARM, PPC and s390x systems. You can also deploy several other operating systems to your machines, including CentOS 7, CentOS 6, Windows, RHEL, and ESXi images, via <a href="https://www.ubuntu.com/support" rel="nofollow noopener">Ubuntu Advantage</a>.

![images-menu-screen|690x351](upload://5FNvhMcRE6ultqCFKHSwdCYr2p3.jpeg) 

<h3 id="heading--vm-hosts">VM hosts</h3>

[VM hosts]( /t/introduction-to-vm-hosting/1524) can give you greater control over your hardware.  A VM host is a collection of individual virtual machines.  You can use a VM host to compose machines into an abstraction of resources that functions like a physical machine – without building one! It’s easy to add a VM host: click the "Add KVM" button on the "KVM" page of the web UI, give the VM host a name, and select "Virsh" as the VM host type.  You will also need to enter the address for the [libvirt](https://ubuntu.com/server/docs/virtualization-libvirt) controlled pool below.

After you create a VM host, you compose hardware by selecting the VM host, and then selecting "Compose" from the Action menu. You can configure composed hardware as desired, including the number of cores, CPU speed, RAM and combined storage.  Then just click "Compose machine", and MAAS will combine resources to create a new, single entity that can be used just like any other machine.  VM hosts abstract multiple resources:

![pod1|690x398](upload://sb4UXWvA9g9kLJEFje8EpHF2gnM.jpeg) 

into pools of composable hardware:

![compose-machine|690x422](upload://gRxwRIdZM3LYiIw9iLc4LoDKKjB.jpeg) 

There you have it: A quick tour of MAAS and its capabilities.  Read on through the documentation to learn more.