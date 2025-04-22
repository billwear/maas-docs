You can get a feel for MAAS without committing real hardware by using virtual machines. This article will show you how, focusing on the web UI.  You should realise that this is not an exhaustive introduction, but it will set you up with a good MAAS test configuration.

<h2 id="heading--installation">Installation</a></h2>

We recommend testing MAAS with [libvirt](https://ubuntu.com/server/docs/virtualization-libvirt) and its VM/QEMU hypervisor driver. The majority of Linux distributions, including Ubuntu, support this tool. It is easy to install</a>, and MAAS natively supports power management of VMs created in libvirt. We also recommend that you install the GUI-based `virt-manager` to ease configuration, but you can use `libvirt` from the command line.

[note]
MAAS also works in a Linux virtual machine running from Oracle’s [VirtualBox](https://www.virtualbox.org).  VirtualBox may be useful if you want to test MAAS from a Microsoft Windows or macOS environment.
[/note]

[Snaps](https://snapcraft.io/docs) are containerised software packages. To install MAAS from a snap simply enter the following:
 
    $ sudo snap install maas --channel=2.8

After entering your password, the snap will download and install from the 2.8 channel. However, MAAS needs initialising before it's ready to go.

We want to provide a more compact version for those who may be testing MAAS.  To achieve this, we're providing a separate snap, called `maas-test-db`, which provides a PostgreSQL database for use in testing and evaluating MAAS.   The following instructions will help you take advantage of this test configuration.

Once it is installed, you can use the `--help` flag with `maas init` to get these instructions:

    $ sudo maas init --help
    usage: maas init [-h] {region+rack,region,rack} . . .
    
    Initialise MAAS in the specified run mode.
    
    optional arguments:
      -h, --help            show this help message and exit
    
    run modes:
      {region+rack,region,rack}
        region+rack         Both region and rack controllers
        region              Region controller only
        rack                Rack controller only
    
    When installing region or rack+region modes, MAAS needs a
    PostgreSQL database to connect to.
    
    If you want to set up PostgreSQL for a non-production deployment on
    this machine, and configure it for use with MAAS, you can install
    the maas-test-db snap before running 'maas init':
        sudo snap install maas-test-db
        sudo maas init region+rack --database-url maas-test-db:///

We'll quickly walk through these instructions to confirm your understanding.  First, install the `maas-test-db` snap:

    sudo snap install maas-test-db

Note that this step installs a a running PostgreSQL and a MAAS-ready database instantiation.  When it's done, you can double check with a built in PostgreSQL shell:

    $ maas-test-db.psql
    psql (10.6)
    Type "help" for help.
    
    postgres=# \l

This will produce a list of databases, one of which will be `maasdb`, owned by `maas`.  Note that this database is still empty because MAAS is not yet initialized and, hence, is not yet using the database.  Once this is done, you can run the `maas init` command:

    sudo maas init region+rack --database-uri maas-test-db:///

After running for a moment, the command will prompt you for a MAAS URL; typically, you can use the default:

    MAAS URL [default=http://10.45.222.159:5240/MAAS]:

When you've entered a suitable URL, or accepted the default, the following prompt will appear:

    MAAS has been set up.
    
    If you want to configure external authentication or use
    MAAS with Canonical RBAC, please run
    
      sudo maas configauth
    
    To create admins when not using external authentication, run
    
      sudo maas createadmin

Let's assume you just want a local testing user named `admin`:

    $ sudo maas createadmin
    Username: admin
    Password: ******
    Again: ******
    Email: admin@example.com
    Import SSH keys [] (lp:user-id or gh:user-id): gh:yourusername

At this point, MAAS is basically set up and running.  You can confirm this with `sudo maas status`.  If you need an API key, you can obtain this with `sudo maas apikey --username yourusername`.  Now you will be able to test and evaluate MAAS by going to the URL you entered or accepted above and entering your `admin` username and password.

<h3>Next steps</h3>

Once you have completed this process, you can access the web UI from the default URL (above) and begin your [configuration journey](https://maas.io/docs/configuration-journey).