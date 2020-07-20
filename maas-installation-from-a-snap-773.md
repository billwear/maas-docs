MAAS can be installed in either of two configurations:  test or production.  The test configuration uses a small PostgreSQL database (in a separate snap), designed for use with MAAS. The full-up production configuration uses a separate PostgreSQL database for performance and scalability.  This article will walk you through both install methods.

* [How do I install (but not initialize) the MAAS snap?](/t/maas-installation-from-a-snap/773#heading--install-maas-snap)
* [How do I upgrade my 2.7 snap to version 2.8?](/t/maas-installation-from-a-snap/773#heading--upgrade-maas-snap)
* [What are MAAS initialisation modes?](/t/maas-installation-from-a-snap/773#heading--maas-init-modes)
* [How do I initialise MAAS for a test or proof-of-concept configuration?](/t/maas-installation-from-a-snap/773#heading--init-poc)
* [How do I initialise MAAS for a production configuration?](/t/maas-installation-from-a-snap/773#heading--init-prod)
* [How do I install MAAS from packages?](https://discourse.maas.io/t/non-snap-maas-installs/1308)
* [How do I migrate an existing snap install?](/t/tips-tricks-and-traps/1506#heading--migrate-maas)
* [What if I want to manually export the MAAS database to an existing PostgreSQL server?](/t/tips-tricks-and-traps/1506#heading--manual-export)
* [How can I check the service status of my MAAS configuration?](/t/maas-installation-from-a-snap/773#heading--service-status)
* [How do I re-initialise MAAS, if I want to?](/t/maas-installation-from-a-snap/773#heading--reinitialising-maas)
* [How can I discover additional init options?](/t/maas-installation-from-a-snap/773#heading--additional-init-options)
* [Give me an example of initialising MAAS](/t/maas-installation-from-a-snap/773#heading--example)
* [Tell me about the MAAS URL](/t/maas-installation-from-a-snap/773#heading--maas-url)
* [Tell me about the shared secret](/t/maas-installation-from-a-snap/773#heading--shared-secret)

<h2 id="heading--install-maas-snap">Installing MAAS from the snap</h2>

[Snaps](https://snapcraft.io/docs) are containerised software packages. To install MAAS from a snap simply enter the following:

    $ sudo snap install maas --channel=2.8

After entering your password, the snap will download and install from the 2.8 channel -- though MAAS needs initialising before it's ready to go.

<h2 id="heading--upgrade-maas-snap">Upgrading MAAS from 2.7</h2>

If you want to upgrade from a 2.7 snap to 2.8, and you are using a `region+rack` configuration, use this command:

    $ sudo snap refresh --channel=2.8 maas

After entering your password, the snap will refresh from the 2.8 channel.  You will **not** need to re-initialize MAAS.

If you are using a multi-node maas deployment with separate regions and racks, you should first run the upgrade command above for rack nodes, then for region nodes.

<h2 id="heading--maas-init-modes">MAAS initialisation modes</h2>

MAAS supports the following modes, which dictate what services will run on the local system:

| Mode          | Region | Rack | Database | Description                           |
|---------------|--------|------|----------|---------------------------------------|
| `all`*        | X      | X    | X        | Deprecated (see warning below)        |
| `region`      | X      |      |          | Region API server only                |
| `rack`        |        | X    |          | Rack controller only                  |
| `region+rack` | X      | X    |          | Region API server and rack controller |
| `none`        |        |      |          | Deinitializes MAAS and stops services |

[note type="Warning" status="all mode being deprecated"]
The MAAS initialisation mode "all" is [deprecated in MAAS version 2.8.0 and will be removed in MAAS version 2.9.0](https://maas.io/deprecations/MD1).
[/note]

<h2 id="heading--init-poc">Initialising MAAS as a test configuration</h2>

We want to provide a more compact version for those who may be testing MAAS.  To achieve this, we're providing a separate snap, called `maas-test-db`, which provides a PostgreSQL database for use in testing and evaluating MAAS.   The following instructions will help you take advantage of this test configuration.

[note]
If you are using packages, you might benefit from [this article](https://discourse.maas.io/t/setting-up-a-flexible-virtual-maas-test-environment/142), which is a little older, but may apply.
[/note]

Once MAAS is installed, you can use the `--help` flag with `maas init` to get relevant instructions:
 
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
        sudo maas init region+rack --database-uri maas-test-db:///

We'll quickly walk through these instructions to confirm your understanding.  First, install the `maas-test-db` snap:
 
    sudo snap install maas-test-db

Note that this step installs a a running PostgreSQL and a MAAS-ready database instantiation.  When it's done, you can double check with a built-in PostgreSQL shell:

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

<h2 id="heading--configuration-verification">Configuration verification</h2>

After a snap installation of MAAS, you can verify the currently-running configuration with:

    sudo maas config

<h2 id="heading--init-prod">Initialise MAAS for a production configuration</h2>

To install MAAS in a production configuration, you need to setup PostgreSQL, as described below.

<h3 id="heading--pg-setup">Setting up PostgreSQL from scratch</h3>

To set up PostgreSQL, even if it's running on a different machine, you can use the following procedure:

1. You will need to install PostgreSQL on the machine where you want to keep the database.  This can be the same machine as the MAAS region/rack controllers or a totally separate machine.  If PostgreSQL (version 10 or better) is already running on your target machine, you can skip this step. To install PostgreSQL, run these commands:

        sudo apt update -y
        sudo apt install -y postgresql

2. You want to make sure you have a suitable PostgreSQL user, which can be accomplished with the following command, where `$MAAS_DBUSER` is your desired database username, and `$MAAS_DBPASS` is the intended password for that username.  Note that if you're executing this step in a LXD container (as root, which is the default), you may get a minor error, but the operation will still complete correctly.

        sudo -u postgres psql -c "CREATE USER \"$MAAS_DBUSER\" WITH ENCRYPTED PASSWORD '$MAAS_DBPASS'"

3. Create the MAAS database with the following command, where `$MAAS_DBNAME` is your desired name for the MAAS database (typically known as `maas`). Again, if you're executing this step in a LXD container as root, you can ignore the minor error that results.

        sudo -u postgres createdb -O "$MAAS_DBUSER" "$MAAS_DBNAME"

4. Edit `/etc/postgresql/10/main/pg_hba.conf` and add a line for the newly created database, replacing the variables with actual  names. You can limit access to a specific network by using a different CIDR than `0/0`.

        host    $MAAS_DBNAME    $MAAS_DBUSER    0/0     md5

5. You can then initialise MAAS via the following command:

        sudo maas init region+rack --database-uri "postgres://$MAAS_DBUSER:$MAAS_DBPASS@$HOSTNAME/$MAAS_DBNAME"

Don't worry; if you leave out any of the database parameters, you'll be prompted for those details.

<h2 id="heading--service-status">Checking MAAS service status</h2>

You can check the status of running services with:

    sudo maas status

Typically, the output looks something like this:

    bind9                            RUNNING   pid 7999, uptime 0:09:17
    dhcpd                            STOPPED   Not started
    dhcpd6                           STOPPED   Not started
    ntp                              RUNNING   pid 8598, uptime 0:05:42
    postgresql                       RUNNING   pid 8001, uptime 0:09:17
    proxy                            STOPPED   Not started
    rackd                            RUNNING   pid 8000, uptime 0:09:17
    regiond:regiond-0                RUNNING   pid 8003, uptime 0:09:17
    regiond:regiond-1                RUNNING   pid 8008, uptime 0:09:17
    regiond:regiond-2                RUNNING   pid 8005, uptime 0:09:17
    regiond:regiond-3                RUNNING   pid 8015, uptime 0:09:17
    tgt                              RUNNING   pid 8040, uptime 0:09:15

With MAAS installed and initialised, you can now open the web UI in your browser and begin your [Configuration journey](https://maas.io/docs/configuration-journey).

<h2 id="heading--example">Example of MAAS initialisation</h2>

The following demonstrates the `region+rack` mode, a popular initialisation choice for MAAS:

    sudo maas init region+rack

`maas` will ask for the MAAS URL:

    MAAS URL [default=http://10.55.60.1:5240/MAAS]: http://192.168.122.1:5240/MAAS

If you also need to create an admin user, you can use:

    sudo maas createadmin

which takes you through the following exchange:

    Create first admin account:       
    Username: admin
    Password: ******
    Again: ******
    Email: admin@example.com
    Import SSH keys [] (lp:user-id or gh:user-id): lp:petermatulis

[note]
You will use the username and password created above to access the web UI.  If you enter a [Launchpad](https://launchpad.net/) or [GitHub](https://github.com) account name with associated SSH key, MAAS will import them automatically.
[/note]

<h2 id="heading--maas-url">MAAS URL</h2>

All run modes (except `none`) prompt for a MAAS URL, interpreted differently depending on the mode:

-   `region`: Used to create a new region controller.
-   `rack`: Used to locate the region controller.

<h2 id="heading--shared-secret">Shared secret</h2>

The 'rack' and 'region+rack' modes will additionally ask for a shared secret that will allow the new rack controller to register with the region controller.

<h2 id="heading--reinitialising-maas">Reinitialising MAAS</h2>

It is also possible to re-initialise MAAS to switch modes.  For example, to switch from `rack` to `region`:
 
    sudo maas init region

<h2 id="heading--additional-init-options">Additional `init` options</h2>

The `init` command can takes optional arguments. To list them, as well as read a brief description of each, you can enter:

    sudo maas init --help