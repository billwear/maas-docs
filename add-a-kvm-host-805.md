[note type="warning" status="Deprecated page"]
This page has been deprecated in favor of newer terminology and technology.  Please see the [updated page](/t/adding-a-vm-host/1549).
[/note]

After installing MAAS, the 'KVM' page is typically empty:

![kvm-open-screen|638x321](upload://zG2Jg6P7iEbakdEQtGVzm5WcY9l.jpeg) 

<h2 id="heading--25">2.5+</h2>

Once MAAS has enlisted, commissioned, and acquired a newly-added machine, you can deploy it as a KVM host:

![kvmpoddeploy](//discourse.maas.io/uploads/default/original/1X/63904b128941348ac07ec6a40ee12c51748c9f0a.png)

<h3 id="heading--cli">CLI</h3>

Once MAAS has enlisted, commissioned, and acquired a machine, you can deploy it as a KVM host:

``` bash
maas $PROFILE machine deploy <system_id> install_kvm=True
```

<h2 id="heading--manualpre-25">Manual/Pre-2.5</h2>

Setting up a manual KVM host in 2.5 or on an older version of MAAS requires more steps.

<h3 id="heading--set-up-a-maas-libvirt-network">Set up a `maas` libvirt network</h3>

Libvirt by default creates a virtual bridge, `virbr0`, through which VMs communicate with each other and the Internet. DHCP, supplied by libvirt, automatically assigns an IP address to each VM.  However, to enable network booting in MAAS, you’ll need to provide DHCP in MAAS and either:

1.  Disable DHCP on libvirt’s `default` network, or
2.  Create a new libvirt network `maas` with DHCP disabled.

You can set up such a `maas` network like this:

``` bash
cat << EOF > maas.xml
<network>
 <name>maas</name>
 <forward mode='nat'>
   <nat>
     <port start='1024' end='65535'/>
   </nat>
 </forward>
 <dns enable="no" />
 <bridge name='virbr1' stp='off' delay='0'/>
 <domain name='testnet'/>
 <ip address='172.16.99.1' netmask='255.255.255.0'>
 </ip>
</network>
EOF
virsh net-define maas.xml
```

Note that this network also has NAT port forwarding enabled to allow VMs to communicate with the Internet at large. Port forwarding is very useful in test environments.

<h3 id="heading--set-up-ssh">Set up SSH</h3>

For MAAS to successfully communicate with libvirt on your KVM host machine, this example command must succeed from every rack controller as user `maas`:

``` bash
virsh -c qemu+ssh://$USER@$KVM_HOST_IP/system list --all
```

Here, `$USER` is a user on your KVM host who is a member of the `libvirtd` Unix group on the KVM host, and `$KVM_HOST_IP` is the IP of your KVM host.

[note]
It's essential to enforce usage of IP addresses to avoid domain name conflicts, should different controllers resolve the same domain name with different IP addresses. You should also avoid using 127.0.0.1 when running multiple controllers, as it would confuse MAAS.
[/note]

<h4>MAAS package installs</h4>

The `maas` user on your rack controllers will issue all virsh commands. Therefore, you'll need to set up SSH public keys on every rack controller for user `maas`.

If you installed MAAS via packages, first create SSH keys on all rack controllers:

``` bash
sudo chsh -s /bin/bash maas
sudo su - maas
ssh-keygen -t rsa -N ''
```

Next, add the contents of `~maas/.ssh/id_rsa.pub` to the KVM host user's `~$USER/.ssh/authorized_keys`. To accomplish this, log into your KVM host node, via SSH, from a host for which MAAS has a matching public SSH key.

<h4> MAAS snap installs</h4>

If you installed MAAS via snap, then create the needed SSH keys this way:

``` bash
sudo mkdir -p /var/snap/maas/current/root/.ssh
cd /var/snap/maas/current/root/.ssh
sudo ssh-keygen -f id_rsa
```

Finally, you'll need to add `id_rsa.pub` to the `authorized_keys` file in `/home/<kvm-host-user-homedir-name>/.ssh/`,  where `<kvm-host-user-homedir-name>` is the name of your KVM host user.

[note]
Insufficient permissions for `$USER` may cause the `virsh` command to fail with an error such as `failed to connect to the hypervisor`. Check the `$USER` group membership to make sure `$USER` is a member of the `libvirtd` group.
[/note]

<h3 id="heading--add">Add</h3>

Now, add a KVM host by using the 'Add pod' button. Choose 'Virsh (Virtual systems)' from the 'Pod type' drop-down menu.

![kvm-add-screen|690x250](upload://1mHQflonvsii29tTSWDv4vUqzzC.jpeg) 

Here, 'Virsh address' typically looks like the following:

``` no-highlight
qemu+ssh://<kvm host IP>/system
```

[note]
MAAS will automatically discover and store the resources your KVM host contains. Any existing machines will also appear on the 'Machines' page, and MAAS will automatically attempt to commission them.
[/note]

<h4 id="heading--add-with-cli">Add with CLI</h4>

Create a KVM host:

``` bash
maas $PROFILE pods create type=virsh power_address=qemu+ssh://ubuntu@192.168.1.2/system
```

Create a KVM host with [overcommitted resources](#heading--overcommit-resources):

``` bash
maas $PROFILE pods create type=virsh power_address=qemu+ssh://ubuntu@192.168.1.2/system \
        power_pass=example cpu_over_commit_ratio=0.3 memory_over_commit_ratio=4.6
```

Create a KVM host that uses a default [storage pool](/t/kvm-host-storage-pools/809):

``` bash
maas $PROFILE pods create type=virsh power_address=qemu+ssh://ubuntu@192.168.1.2/system \
        power_pass=example default_storage_pool=pool1
```

<h2 id="heading--configuration">Configuration</h2>

KVM hosts have several configuration options. Modify these by selecting the 'Configuration' tab and clicking 'Edit'. Options include a KVM host's location, password, network zone, and default resource pool.

![pod configuration](https://discourse.maas.io/uploads/default/original/1X/e6f9b3effcc9e4f44a09836cf6185449410bae7f.png)

<h3 id="heading--overcommit-resources">Overcommit resources</h3>

Overcommitted resources are those allocated beyond what's available in the physical resource. Using sliders on the configuration page, you can limit whether MAAS will attempt to overcommit CPU and memory. The input fields to the right of the sliders accept floating-point values from 0 to 10, with a default value of 1.

The following shows theoretical examples of these ratios and how they affect physical resource allocation:

-   `8 physical CPU cores  * 1 multiplier     = 8 virtual CPU cores`
-   `8 physical CPU cores  * 0.5 multiplier   = 4 virtual CPU cores`
-   `32 physical CPU cores * 10.0 multiplier  = 320 virtual CPU cores`
-   `128GB physical memory  * 5.5 multiplier  = 704G virtual Memory`

Overcommitting resources allows a user to compose many MAAS-managed machines without worrying about the physical limitations of the host. For example, on a physical host with four cores and 12 GB of memory, you could compose four virsh machines, each using two cores and 4 GB of memory.  This arrangement overcommits the available physical resources. Provided you never run all four simultaneously, you'd have all the benefits of MAAS-managed VMs without over-taxing your host.

<!-- LINKS -->