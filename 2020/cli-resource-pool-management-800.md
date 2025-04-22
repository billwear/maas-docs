This document explains resource-pool management tasks you can perform with the MAAS CLI.

#### Quick questions you may have:

* [How can I create a resource pool?](/t/cli-resource-pool-management/800#heading--creating-a-resource-pool)
* [How can I list available resource pools?](/t/cli-resource-pool-management/800#heading--list-available-resource-pools)
* [How can I list a single resource pool?](/t/cli-resource-pool-management/800#heading--list-a-single-resource-pool)
* [How can I update a resource pool?](/t/cli-resource-pool-management/800#heading--update-a-resource-pool)
* [How can I delete a resource pool?](/t/cli-resource-pool-management/800#heading--delete-a-resource-pool)
* [How can I add a machine to a resource pool?](/t/cli-resource-pool-management/800#heading--add-a-machine-to-a-resource-pool)
* [How can I get started with MAAS CLI?](/t/maas-cli/802)
* [What do I need to know about resource pools?](/t/resource-pools/831)

<h2 id="heading--creating-a-resource-pool">Creating a resource pool</h2>

Here's an example that demonstrates how to create a new resource pool named `myresource`.

``` bash
maas $PROFILE resource-pools create name=myresource description="A new resource pool."
```

[note]
The `description` field is optional.
[/note]

<h2 id="heading--list-available-resource-pools">List available resource pools</h2>

``` bash
maas $PROFILE resource-pools read
```

<h2 id="heading--list-a-single-resource-pool">List a single resource pool</h2>

``` bash
maas $PROFILE resource-pool read $RESOURCE_POOL_ID
```

<h2 id="heading--update-a-resource-pool">Update a resource pool</h2>

``` bash
maas $PROFILE resource-pool update $RESOURCE_POOL_ID name=newname description="A new description."
```

[note]
The `name` and `description` fields are optional.
[/note]

<h2 id="heading--delete-a-resource-pool">Delete a resource pool</h2>

``` bash
maas $PROFILE resource-pool delete $RESOURCE_POOL_ID
```

<h2 id="heading--add-a-machine-to-a-resource-pool">Add a machine to a resource pool</h2>

``` bash
maas $PROFILE machine update $SYSTEM_ID pool=$POOL_NAME
```

<!-- LINKS -->