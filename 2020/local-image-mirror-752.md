Mirroring images is worthy of consideration.  The SimpleStreams protocol delivers Images to MAAS, which is especially useful when your Internet link is slow or unreliable. In this mirrored configuration, images will be instantly available when MAAS requests them.

#### Quick questions you may have:

* [How do I set up a local mirror?](/t/local-image-mirror/752#heading--set-up-local-mirror)
* [How do I configure MAAS to use the local mirror?](/t/select-and-import-images/751#heading--image-mirrors)
* [How do I use a local mirror through the CLI?](/t/local-image-mirror/752#heading--local-mirror-with-cli)

<h2 id="heading--set-up-local-mirror">Setting up a local mirror</h2>

To use mirroring, you begin by installing the necessary software on the host that will house the mirror:

``` bash
sudo apt install simplestreams
```

First define some variables to unclutter eventual CLI commands:

``` bash
KEYRING_FILE=/usr/share/keyrings/ubuntu-cloudimage-keyring.gpg
IMAGE_SRC=https://images.maas.io/ephemeral-v3/daily/
IMAGE_DIR=/var/www/html/maas/images/ephemeral-v3/daily
```

[note]
The v3 format offers only a 'daily' stream whereas in previous versions a 'releases' stream was available. The latter changes far less frequently but lacks security and bug fixes.
[/note]

The below example selects all available kernels that are compatible with either Ubuntu 14.04 (Trusty) and Ubuntu 16.04 (Xenial) for the amd64 architecture, resulting in a download of approximately 3.1 GB. The second command mirrors the bootloaders.

``` bash
sudo sstream-mirror --keyring=$KEYRING_FILE $IMAGE_SRC $IMAGE_DIR \
    'arch=amd64' 'release~(trusty|xenial)' --max=1 --progress
sudo sstream-mirror --keyring=$KEYRING_FILE $IMAGE_SRC $IMAGE_DIR \
    'os~(grub*|pxelinux)' --max=1 --progress
```

To know in advance what the `sstream-mirror` command will grab, or if you want to save bandwidth and time by avoiding bad selections, include the `--dry-run` option. When you are satisfied, remove that option to initiate the download.

MAAS will write the images to disk in the directory defined by the variable 'IMAGE_DIR' above, and the 'location' of the new boot source will be:

`URL=http://<myserver>/maas/images/ephemeral-v3/daily/`

Where `<myserver>` identifies your server's hostname or IP address.

Verify the availability of the images by visiting the above URL.

The final `sstream-mirror` command should be invoked at regular intervals (i.e. with `cron`) to ensure the mirror contains the latest images.

<h2 id="heading--local-mirror-with-cli">Configure MAAS to use a local mirror with the CLI</h2>

See [Add a boot source](/t/cli-image-management/797#heading--add-a-boot-source) and [Using a local image mirror](/t/cli-image-management/797#heading--using-a-local-image-mirror) for instructions on how to do this with the CLI.

<!-- LINKS -->