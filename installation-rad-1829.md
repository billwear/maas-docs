[note type="negative" status="Deprecated/out of date page"]
Some of the methods and information in this page are out of date or have been deprecated.  Installation from a snap and from packages still works, and these installs still work if done in LXD containers --  but by following the instructions in the [current installation page](/t/maas-installation-from-a-snap/773).   Installation from ISO has been deprecated and likely will no longer work.  This page has been left inline for continuity, but its accuracy and relevance is no longer guaranteed.
[/note]

There are four ways to install MAAS:

-   [From a snap](/t/install-from-a-snap/773). Install via a snap.
    -   Versatile: Put components where you want them (centralized or distributed)
    -   Can access developmental versions of MAAS
    -   Application isolation
-   From an Ubuntu Server ISO. Install a complete MAAS environment or a rack controller during the ISO installation of Ubuntu Server.
    -   Recommended for new MAAS environments, especially for new users
    -   Enables you to get started as quickly as possible
-   [From packages](/t/non-snap-maas-installs/1308). Install packages for individual MAAS components.
    -   Versatile: Put components where you want them (centralized or distributed)
    -   Can access developmental versions of MAAS
-   With LXD. Create a self-contained MAAS environment with LXD containers.
    -   MAAS nodes also run as local containers
    -   Ideal for testing and experimenting with MAAS
    -   Can access developmental versions of MAAS

Ubuntu 16.06 LTS systems running MAAS 2.3 or older can upgrade to MAAS 2.4 via an LTS-to-LTS upgrade to Ubuntu 18.04. See [Upgrade 2.3 to 2.4 from Ubuntu 16.04](/t/upgrade-2-3-to-2-4-from-ubuntu-16-04/777) for details.

<!-- LINKS -->