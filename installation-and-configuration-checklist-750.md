This is a guide that you can use to achieve a working MAAS environment. Once you perform a step come back here (using the menu in the left pane) to continue on to the next one.

<h2 id="heading--software-installation">Software installation</h2>

As explained in the [Introduction](/t/maas-documentation/25#heading--key-components-and-colocation-of-all-services), the installation of MAAS consists of the installation of a rack controller and a region controller which, in turn, provide a multitude of services. Go ahead and [install MAAS](/t/install-from-a-snap/773)!

<h2 id="heading--access-the-web-ui">Access the web UI</h2>

You *will* be using the web UI so now is the time to log in and take a look around! Proceed to the [web UI](/t/web-ui/782) now. This will involve the creation of an administrator user. Notice how the web UI (API server) is accessed via port 5240 and not port 80.

[note]
Although the web UI may be accessed via port 80, this is not guaranteed to work in future versions of MAAS.
[/note]

The initial access of the web UI will kick off an optional [configuration journey](/t/configuration-journey/781) whose purpose is to get the essential aspects of MAAS configured right away.

Completing the journey will remove the need to perform equivalent deeds below so you'll need to adjust accordingly.

<h2 id="heading--zones">Zones</h2>

For [zones](/t/availability-zones/820), many people won't need to change anything as a default zone is provided out of the box. However, they are still worth reading about, especially if the default one does not suffice.

<h2 id="heading--networking">Networking</h2>

Firstly, in terms of IP addresses, understand what a *reserved range* is by reading [Concepts and terms](/t/concepts-and-terms/785#heading--ip-ranges). Create one (not *reserved dynamic range*) if you need one.

Secondly, configure a default gateway and a nameserver that your machines will use. See [Networking](/t/networking/768) for how to do this.

<h2 id="heading--import-boot-images">Import boot images</h2>

[Read up on images](/t/images/754) as they're quite important. Continue reading until you have discovered how to import them. You will see that you have the choice to use the CLI to do this. Either way: achievement unlocked!

The import process can take a while. Consider moving on and coming back. Just ensure that the import has completed prior to adding a machine.

[note]
Once installed, by default, MAAS will begin downloading images for the latest Ubuntu LTS.
[/note]

<h2 id="heading--access-the-maas-cli">Access the MAAS CLI</h2>

Even if you've imported images with the web UI, it would be wise (and cool?) to give the [CLI](/t/maas-cli/802) a spin in case you ever need to use it later. Although we strive to make the web UI feature-equivalent to the CLI, some things can still only be done with the CLI.

<h2 id="heading--enable-dhcp">Enable DHCP</h2>

You won't get far without DHCP since it is required in order to make PXE work, which, in turn, is necessary to introduce your systems to MAAS. But you knew that. Anyway, DHCP is installed - it just needs enabling. [Read about DHCP](/t/dhcp/759) and continue until you have enabled it.

<h2 id="heading--users-and-ssh-keys">Users and SSH keys</h2>

You already have an administrative user but MAAS can also have regular users (who log in to the interface or use the CLI). What users you create depends on how you intend to use MAAS.

Additionally, in order for a user to log into a MAAS-deployed machine that user *must* have their public SSH key installed on it.

Study [User accounts](/t/user-accounts/790) to learn about how to create more users and how to add their public SSH keys to MAAS. Once that's done, every deployed machine will automatically have that key installed.

<h2 id="heading--add-a-node">Add a machine</h2>

It's time to actually do something! MAAS manages machines, but at this time it doesn't have any. Go forth and [add a machine](/t/add-nodes/821) now. Obviously, you need a spare physical system (but KVM works too). In the web UI, confirm that the import of images has finished!

Go to the 'Machines' page in the web UI. A successfully added machine will soon appear there with a status of *New*. It will also have a funny name. Whatever, you're still a rock star!

<h2 id="heading--edit-power-type">Edit power type</h2>

A machine needs to power cycle while being managed by MAAS. The next step is therefore to tell MAAS how to do this. That is, you need to [edit the power type](/t/bmc-power-types/830) of the machine's BMC.

<h2 id="heading--commission-a-node">Commission a machine</h2>

Commissioning a machine involves MAAS testing it to ensure that it is able to communicate properly with the region API server. [Commission](/t/commission-nodes/822) your machine now.

<h2 id="heading--deploy-a-node">Deploy a machine</h2>

Lots of folks would have [Juju](https://jujucharms.com/docs/stable/about-juju) take over at this point. Juju acts as a sort of command & control centre for adding services/applications on top of MAAS machines (among other "clouds"). If you're just not there and/or you want to quickly test things out you can use the web UI to [deploy a machine](/t/deploy-nodes/825) directly.

<h2 id="heading--ssh-to-the-node">SSH to the machine</h2>

If you [imported your SSH key](/t/user-accounts/790#heading--ssh-keys) then you should now be able to ssh to the deployed machine by connecting to the 'ubuntu' account. The machine's page in the web UI will inform you of its IP address. Mission accomplished!

<!-- LINKS -->