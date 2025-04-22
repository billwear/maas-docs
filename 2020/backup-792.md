MAAS doesn't yet include specific tools to back up and restore a working MAAS configuration. MAAS servers are part of your data centre, just like other Linux-based servers, so your current backup and disaster recovery solution will probably back up your MAAS environment.  Even so, you should know which files and actions are critical -- to ensure that you get a clean backup, and further ensure that you can restore it cleanly.

#### Quick questions you may have:

* [What configuration files should I capture in my backup?](/t/backup/792#heading--configuration-files)
* [How should I prepare PostgreSQL data for backup?](/t/backup/792#heading--postgresql-export)
* [What services should I stop before backing up?](/t/backup/792#heading--stop-critical-services)
* [How should I archive files to make the backup more efficient?](/t/backup/792#heading--archive-configuration-files)
* [How do I restore my system, should the need arise?](/t/backup/792#heading--restore-files)
* [What steps should I take to restore the database to normal operation?](/t/backup/792#heading--recreatingupdating-the-db)

<h2 id="heading--configuration-files">Configuration files</h2>

The following MAAS components need to be backed-up and restored, on each region and rack controller, to recreate a working environment:

1.  The PostgreSQL database
2.  The configuration files in `/etc/maas`
3.  The configuration files in `/var/lib/maas`

`/var/lib/maas/boot-resources` can safely be excluded as this contains images easily re-downloaded within MAAS.

Other configuration files, such as those used by your network configuration (`/etc/network/interfaces`, for example) will need to be backed-up and restored according to your specific deployment requirements.

<h2 id="heading--postgresql-export">PostgreSQL export</h2>

The following procedure involves some assumptions: 

* you have installed region and rack controllers on the same machine. 
* you have installed MAAS on Ubuntu 18.04 LTS (Bionic).
* you are restoring software an identical hardware and network configuration.

To backup your PostgreSQL database to a file called `dump.sql` in your home directories, enter the following:

``` bash
sudo -u postgres pg_dumpall -c > ~/dump.sql
```

If you run the above `pg_dumpall` process in the background, you can ensure this has completed and that there are no other established sessions with the following command:

``` bash
sudo -u postgres psql -c  "SELECT * FROM pg_stat_activity"
```

Running sessions, such as pg_dumpall, will appear in the `application_name` column of the output alongside `psql` running the above `pg_stat_activity` query. Excepting psql, if `application_name` is empty, you can safely stop the database service.

<h2 id="heading--stop-critical-services">Stop critical services</h2>

To avoid conflicting updates during a backup, stop the following services with the `sudo systemctl stop <service>` command:

-   postgresql.service
-   maas-dhcpd.service
-   maas-rackd.service
-   maas-regiond.service

<h2 id="heading--archive-configuration-files">Archive configuration files</h2>

Archive the database and the required configuration files with a command similar to the following:

``` bash
sudo tar cvpzf ~/backup.tgz --exclude=/var/lib/maas/boot-resources /etc/maas /var/lib/maas ~/dump.sql
```

Make sure you move the resulting `backup.tgz` to some external storage you can access when restoring the system.

We've now backed up all the components necessary to recreate a MAAS deployment. Next, we'll discuss how to restore this configuration.

<h2 id="heading--restore-files">Restore files</h2>

Start with a freshly-updated installation of Ubuntu on identical hardware. Reinstall MAAS via the standard procedure (`sudo apt install maas`), then stop the following services (PostgreSQL needs to keep running):

-   maas-dhcpd.service
-   maas-rackd.service
-   maas-regiond.service

Copy the backup file to the new machine and untar its contents (`sudo tar xvzpf backup.tgz`).

To restore the state of the database, enter the following from the backup directory:

``` bash
sudo -u postgres psql -f dump.sql postgres
```

Next, copy across the old configuration files to their new locations, taking care to move the originals aside just in case:

``` bash
sudo mv /etc/maas /etc/_maas; mv /var/lib/maas /var/lib/_maas
sudo cp -prf etc/maas /etc/; cp -prf var/lib/maas /var/lib/
```

[note]
Take care to preserve the correct permissions when restoring files and directories.
[/note]

If your restore process regenerated the `/var/lib/maas/secret` file, make sure update this secret on any additional rack controllers.

<h2 id="heading--recreatingupdating-the-db">Recreating/updating the DB</h2>

When you restore a backup, you'll have to "upgrade" the DB schema to re-create DB triggers or ensure that the schema matches the currently-running version.

MAAS relies on various DB triggers for multiple operations. As such, it is always required to re-create those after restoring from a backup. Similarly, newer versions of MAAS may have new or missing migrations, and merely restoring a backup may not be enough to restore normal operation.

As such, it is required to re-create the DB triggers (or upgrade the DB and run new/missing migrations) with the following command:

``` bash
sudo maas-region dbupgrade
```

[note]
Please note to run this command on one of the Region Controllers (if this is a multi-region MAAS cluster).
[/note]

Now either restart your system(s) or the stopped services. You'll find your MAAS deployment fully restored.