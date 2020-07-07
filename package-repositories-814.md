Package repositories managed within MAAS can be of two types:

-   Ubuntu package repositories
-   Personal Package Archives (PPA)

You can configure repositories in the 'Package repositories' tab on the 'Settings' page. Any enabled repository listed on this page will become automatically available to any subsequently deployed nodes.

MAAS further simplifies the addition of third-party repositories by also allowing the administrator to input their respective GPG keys here. This arrangement means that nodes will have instant access to these repositories (i.e. no need to import the keys into APT).

An added repository can be disabled and re-enabled using a toggle switch to the right of it.

<h2 id="heading--ubuntu-package-repositories">Ubuntu package repositories</h2>

An Ubuntu package repository is a repository that makes available Ubuntu packages to computers able to connect to it over the network, whether that network is private or public (e.g. the Internet).

MAAS comes equipped with the official Ubuntu repository `archive.ubuntu.com` as well as the equivalent for architectures other than i386 and amd64: `ports.ubuntu.com` as is evident in the default configuration below:

![default repositories config](https://assets.ubuntu.com/v1/77b93794-manage-repositories__2.4_default-repo-config.png)

Adding a third-party repository is elementary. Begin by basing the configuration on a line you would typically place in a system's `/etc/apt/sources.list` file. For instance, for the Google Chrome repository, the line would look like:

`deb http://dl.google.com/linux/chrome/deb stable main`

You will also need the GPG public key that is associated with the private key that signed this particular repository. Typically, the project's website is consulted to obtain this information. For this example, you could download the key like this:

``` bash
wget https://dl.google.com/linux/linux_signing_key.pub
```

The key now resides in the saved file `linux_signing_key.pub` for later use.

To add this repository, then, hit the 'Add repository' button and fill in the fields using the gathered information. Note that the 'Name' is an arbitrary label to give the repository.

Before saving, the form should look very similar to this:

![add repository](https://assets.ubuntu.com/v1/1aa1c512-manage-repositories__2.4_add-repo.png)

Click 'Add repository' to save the configuration.

A private repository can be built to assist with offline operations, based on the official repository. This repository can also contain custom packages.

<h2 id="heading--personal-package-archives-ppa">Personal Package Archives (PPA)</h2>

A Personal Package Archive (PPA) is a [Launchpad](https://launchpad.net)-based method for any individual (or team) to build and distribute packages for Ubuntu.

Adding a PPA is equally straightforward. Using the [`sosreport` PPA](https://launchpad.net/~canonical-support/+archive/ubuntu/support-tools) as an example, first acquire the PPA's address from its page on Launchpad:

`ppa:canonical-support/support-tools`

Like before, a public GPG key will be needed. Also get this from the PPA's Launchpad page: 'Technical details about this PPA' &gt; '1024R/9360754F' &gt; '9360754F'.

To add this PPA, then, hit the 'Add repository' button and fill in the fields. Before saving, the form should look something like this:

![add PPA](https://assets.ubuntu.com/v1/a0962e17-manage-repositories__2.4_add-ppa.png)

Click 'Add repository' to save the configuration.

See [Launchpad PPAs](https://help.launchpad.net/Packaging/PPA) for more information on PPAs.

<!-- LINKS -->