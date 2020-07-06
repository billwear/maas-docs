[note type="warning" status="Procedure for versions 2.7 and below only"]
This page covers the obsoleted use of deb/APT packages to install MAAS.  With the advent of MAAS 2.7.0, installation via [snap](/t/install-from-a-snap/773) is the recommended method to acquire MAAS.  Please note that using this method with versions 2.8 and above is not recommended.
[/note]  

You may want to consider an installation within [LXD containers](/t/install-with-lxd/757). These containers allow MAAS nodes to also run as local containers and are ideal for testing and experimenting with MAAS.

<h2 id="heading--packages">Packages</h2>

There are three packages to consider when installing MAAS:

- `maas-region-controller` - region API server, database, DNS, [HTTP proxy](/t/proxy/763), and [web UI](/t/web-ui/782)
- `maas-rack-controller` - [rack controller](/t/rack-controller/771) and [DHCP](/t/dhcp/759)
- `maas` - a metapackage that installs both the above packages to provide a complete MAAS environment

Each of the above packages has its dependencies. That is, each will bring in other MAAS packages not listed above. You can see the full list of MAAS packages with the command:

``` bash
apt-cache search maas
```

As an example, running this command might produce output that looks like this:

![apt-cache-maas-output|690x339](upload://giaZfenWDEkils5KCgA6BlgC6L.jpeg) 

The 'maas' metapackage is the recommended way to install MAAS. See the [Introduction](/t/what-is-maas/840#heading--key-components-and-colocation-of-all-services) for more detail on colocating all services on a single host.  Note that high availability with MAAS involves installing multiple region/rack controllers. See [MAAS HA](/t/high-availability/804) for more information on this topic.

<h2 id="heading--package-repositories">Package repositories</h2>

While MAAS is available in the normal Ubuntu archives, the available packages may be lagging non-archive, but still stable, versions.  For example, you can check the available versions of MAAS by typing: 

``` bash
apt-cache madison maas
```

which produces output similar to this:

![apt-cache-madison-output|690x100](upload://vbYbb4hTxUaiufHj4aErENwkVAh.jpeg) 

You can install a newer stable version via PPAs listed on the [MAAS launchpad](https://launchpad.net/~maas), including:

-   [ppa:maas/2.6](https://launchpad.net/~maas/+archive/ubuntu/2.6)
-   [ppa:maas/2.7](https://launchpad.net/~maas/+archive/ubuntu/2.7)

For example, to add the 2.7 PPA, type:

``` bash
sudo apt-add-repository -yu ppa:maas/2.7
```

which produces output similar to:

![add-maas-2-7-ppa|690x494](upload://p65sJ6uRq2w22SFfxvLze2dEjsP.jpeg) 

If you check available versions again, you will see that the new repository is now available:

![apt-cache-madison-2-7|690x126](upload://1ukIlIJPuPTYDZa2STEcNGJF1hv.jpeg) 

<h2 id="heading--installation-scenarios">Installation scenarios</h2>

The recommended way to set up an initial MAAS environment is to put everything on one machine:

``` bash
sudo apt install maas
```

Executing this command leads you to a list of dependent packages to be installed, and a summary prompt that lets you choose whether to continue with the install:

![apt-install-maas-y-n|522x499](upload://26gNdi5vdnCMEDqgO9bp2xXz68R.jpeg) 

Choosing "Y" proceeds with a standard <code>apt</code> package install.

<h3>Distributed environment</h3> 

<p>For a more distributed environment, you can place the region controller on one machine:</p>

``` bash
sudo apt install maas-region-controller
```

and the rack controller (see [Rack controller](/t/rack-controller/771) for details) on another:

``` bash
sudo apt install maas-rack-controller
sudo maas-rack register
```

These two steps will lead you through two similar <code>apt</code> install sequences.

<h2 id="heading--creating-a-maas-user">Creating a MAAS user</h2>

<p>Finally, you will need to create a MAAS administrator user to access the web UI:</p>

``` bash
sudo maas createadmin --username=$PROFILE --email=$EMAIL_ADDRESS
```

<p>For example, the process might go like this:</p>

![apt-create-admin-sequence|690x67](upload://72BsWNvix7Wfm45vFLbMIjV6WBX.jpeg) 
<p>The username can be anything. You will also be prompted to supply a password for the user. The command option <code>--password=$PASSWORD</code> can be used to specify one but, depending on your environment, this may pose a security risk.</p>
<div class="p-notification">
<p class="p-notification__response">At this time, MAAS does not make use of the email address. However, it may do so in the future.</p>
</div>

Finally, the <code>createadmin</code> option asks for an SSH key:

![create-admin-ssh-key-prompt|475x26](upload://a9E7n9qKDwZCeuDvLKwyv3imTXE.jpeg) 

<p>If you have an SSH key associated with your launchpad or github accounts, you can enter the username here to include the key.  For launchpad, just enter <code>lp:username</code>, and for github, enter <code>gp:username</code> at the prompt.  In both cases, the actual username has to be supplied after the <code>lp:</code> or <code>gh:</code> prefix. </p>

<p>If you don't have a key associated with either of these services, you will have an opportunity to paste your public key into the MAAS SSH key list, after you've started MAAS for the first time as part of the welcome screens.</p>

Once all this is done, you're ready to access MAAS from the web UI and begin your [Configuration journey](https://maas.io/docs/configuration-journey).