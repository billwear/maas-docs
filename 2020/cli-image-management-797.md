The following is a list of image management tasks to perform with the MAAS CLI.

#### Quick questions you may have:

* [How do I get started with the MAAS CLI?](/t/maas-cli/802)
* [What might I need to know about images?](/t/images/754)
* [How do I list boot sources?](/t/cli-image-management/797#heading--list-boot-sources)
* [How do I select images?](/t/cli-image-management/797#heading--select-image)
* [How do I list image selections?](/t/cli-image-management/797#heading--list-image-selections)
* [How do I import newly-selected images?](/t/cli-image-management/797#heading--import-newly-selected-images)
* [How do I list currently available images?](/t/cli-image-management/797#heading--list-currently-available-images)
* [How do I delete a boot source?](/t/cli-image-management/797#heading--delete-a-boot-source)
* [How do I edit a boot source?](/t/cli-image-management/797#heading--edit-a-boot-source)

<h2 id="heading--list-boot-sources">List boot sources</h2>

If you want to download boot sources, i.e., the locations from which you may download images, try the following:

``` bash
maas $PROFILE boot-sources read
```

[note]
Although multiple boot sources may be listed, MAAS can only practically work with a single boot source.
[/note]

<h2 id="heading--select-images">Select images</h2>

Use the `boot-source-selections` command to select images from a boot source. After selecting new images, you will need to [import](#heading--import-newly-selected-images) them.

``` bash
maas $PROFILE boot-source-selections create $SOURCE_ID \
    os="ubuntu" release="$SERIES" arches="$ARCH" \
    subarches="$KERNEL" labels="*"
```

For example, to select all kernels for 64-bit Trusty from a boot source with an id of '1':

``` bash
maas $PROFILE boot-source-selections create 1 \
    os="ubuntu" release="trusty" arches="amd64" \
    subarches="*" labels="*"
```

<h3 id="heading--hardware-enablement-hwe">Hardware enablement (HWE)</h3>

For example, to get just the latest amd64 HWE kernel available for Trusty, which, at time of writing, is from Xenial:

``` bash
maas $PROFILE boot-source-selections create 1 \
    os="ubuntu" release="trusty" arches="amd64" \
    subarches="hwe-x" labels="*"
```

For Xenial kernels (and starting with MAAS 2.1), notation has changed. To select the latest amd64 HWE kernel available for Xenial:

``` bash
maas $PROFILE boot-source-selections create 1 \
    os="ubuntu" release="xenial" arches="amd64" \
    subarches="hwe-16.04" labels="*"
```

<h2 id="heading--list-image-selections">List image selections</h2>

To list image selections for a boot source:

``` bash
maas $PROFILE boot-source-selections read $SOURCE_ID
```

<h2 id="heading--import-newly-selected-images">Import newly-selected images</h2>

To import newly-selected images (boot resources):

``` bash
maas $PROFILE boot-resources import
```

Once newly-selected images are imported, a sync mechanism is enabled (by default) to keep them up to date. The refresh time interval is 60 minutes.

Available images resulting from this action are reflected in the web UI.

<h2 id="heading--list-currently-available-images">List currently available images</h2>

To list currently available/imported images (boot resources):

``` bash
maas $PROFILE boot-resources read
```

<h2 id="heading--delete-a-boot-source">Delete a boot source</h2>

To delete a boot source (the location from which you can download images): 

``` bash
maas $PROFILE boot-source delete $SOURCE_ID
```

If you delete the sole boot source, then the fields 'Sync URL' and 'Keyring Path' in the web UI will take on null values.

<h2 id="heading--edit-a-boot-source">Edit a boot source</h2>

You can edit an existing boot source by changing the GPG keyring file ($KEYRING_FILE) and the location ($URL).

Update the boot source:

``` bash
maas $PROFILE boot-source update $SOURCE_ID \
    url=$URL keyring_filename=$KEYRING_FILE
```

At this time MAAS only supports a boot source containing official MAAS images. As a result, you can only edit a boot source if you have set up a mirror of its images. The location can change, but the keyring remains constant:

KEYRING_FILE=/usr/share/keyrings/ubuntu-cloudimage-keyring.gpg

<h2 id="heading--add-a-boot-source">Add a boot source</h2>

[note]
To avoid unnecessary complexity, you should probably delete any existing boot sources before adding a new one.
[/note]

Presented below are two use cases for adding a boot source:

1.  Use a local image mirror (official images)
2.  If you deleted the default image, recreate it

The general syntax is:

``` bash
maas $PROFILE boot-sources create \
    url=$URL keyring_filename=$KEYRING_FILE
```

The output will include a new numeric ID that identifies the boot source ($SOURCE_ID).

Since MAAS can only practically work with a single boot source, so you will need to delete any existing sources. Note that the location (URL) is the only variable. The only supported keyring is:

KEYRING_FILE=/usr/share/keyrings/ubuntu-cloudimage-keyring.gpg

If you added a sole boot source, then the fields 'Sync URL' and 'Keyring Path' in the web UI will reflect its values.

Once the source is added, proceed to the [Select and import](/t/select-and-import-images/751) images step.

<h3 id="heading--using-a-local-image-mirror">Using a local image mirror</h3>

Once the mirror is set up according to [Local image mirror](/t/local-image-mirror/752) it is just a matter of specifying the mirror location (URL). Since the images come from the default source, you should use the default keyring. If you are following the above mirror document, the variable values should be:

-   URL=https://$MIRROR/maas/images/ephemeral-v3/daily/
-   KEYRING_FILE=/usr/share/keyrings/ubuntu-cloudimage-keyring.gpg

Where $MIRROR is the mirror server's hostname or IP address.

<h3 id="heading--recreate-the-default-boot-source">Recreate the default boot source</h3>

Recreate the default boot source if it was ever deleted using the following variable values:

-   URL=https://images.maas.io/ephemeral-v3/daily/
-   KEYRING_FILE=/usr/share/keyrings/ubuntu-cloudimage-keyring.gpg

<!-- LINKS -->