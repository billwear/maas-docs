This document lists tag management tasks you can perform with the MAAS CLI.

#### Quick questions you may have:

* [How do I get started with the MAAS CLI?](/t/maas-cli/802)
* [What are tags and how do I use them?](/t/maas-tags/834)
* [How do I effect rudimentary tag creation?](/t/cli-tag-management/801#heading--rudimentary-tag-creation)
* [How do I do tag creation and auto-assignment?](/t/cli-tag-management/801#heading--tag-creation-and-auto-assignment)
* [How do I delete a tag?](/t/cli-tag-management/801#heading--delete-a-tag)
* [How do I list all tags?](/t/cli-tag-management/801#heading--list-all-tags)
* [How do I list nodes/machines labelled with a tag?](#heading--list-nodesmachines-labelled-with-a-tag)
* [How do I handle Juju integration?](/t/cli-tag-management/801#heading--juju-integration)
* [How do I effect manual tag assignment?](/t/cli-tag-management/801#heading--manual-tag-assignment)
* [How do I effect hybrid tag assignment?](/t/cli-tag-management/801#heading--hybrid-tag-assignment)

<h2 id="heading--rudimentary-tag-creation">Rudimentary tag creation</h2>

``` bash
maas $PROFILE tags create name=$TAG_NAME
```

<h2 id="heading--tag-creation-and-auto-assignment">Tag creation and auto-assignment</h2>

When you supply a [definition](/t/maas-tags/834#heading--tag-definitions) during a tag's creation, MAAS automatically applies the tag o all the nodes that satisfy the definition:

``` bash
maas $PROFILE tags create name=$TAG_NAME \
    comment='$TAG_COMMENT' definition='$TAG_DEFINITION'
```

For example,

``` bash
maas $PROFILE tags create name='gpu' \
    comment='GPU with clock speed >1GHz for running CUDA type operations.' \
    definition='//node[@id="display"]/'clock units="Hz"' > 1000000000'
```

We recommend that each tag have a short name and a comment that adequately describes it. You should make sure to create both of these because they will help you identify and use the tag later.

<h2 id="heading--delete-a-tag">Delete a tag</h2>

``` bash
maas $PROFILE tag delete $TAG_NAME
```

<h2 id="heading--list-all-tags">List all tags</h2>

To list all tags present on the region controller:

``` bash
maas $PROFILE tags read
```

<h2 id="heading--list-nodesmachines-labelled-with-a-tag">List nodes/machines labelled with a tag</h2>

To list what nodes (or machines) a tag applies to:

``` bash
maas $PROFILE tag nodes $TAG_NAME
maas $PROFILE tag machines $TAG_NAME
```

<h2 id="heading--juju-integration">Juju integration</h2>

Although you can use a tag in the web UI (as a node search filter), you realise most of the benefit when you're deploying applications with Juju.

For example, to use the 'gpu' tag to deploy a (hypothetical) service called 'cuda':

``` bash
juju deploy --constraints tags=gpu cuda
```

You can also use multiple tags in addition to the normal Juju constraints:

``` bash
juju deploy --constraints "mem=1024 tags=gpu,intel" cuda
```

<h2 id="heading--manual-tag-assignment">Manual tag assignment</h2>

It is possible to assign tags to nodes manually by simply omitting the definition and applying the tag to a node by referencing its system id:

``` bash
maas $PROFILE tags create name=$TAG_NAME comment='$TAG_COMMENT'
maas $PROFILE tag update-nodes $TAG_NAME add=$SYSTEM_ID
```

To remove a tag:

``` bash
maas $PROFILE tag update-nodes $TAG_NAME remove=$SYSTEM_ID
```

In the same operation, a tag can be added to some nodes and removed from others:

``` bash
maas $PROFILE tag update-nodes $TAG_NAME \
    add=$SYSTEM_ID_1 add=$SYSTEM_ID_2 remove=$SYSTEM_ID_3
```

<h2 id="heading--hybrid-tag-assignment">Hybrid tag assignment</h2>

You can also create a tag with a definition (thereby map to certain nodes), remove the definition (but retain the mapping), and then add the tag manually to specific nodes. This action is useful for hardware which is conceptually similar but does not all satisfy a single tag definition. Here are the commands you would use to do this:

``` bash
maas $PROFILE tags create name=$TAG_NAME \
    comment='$TAG_COMMENT' definition='$TAG_DEFINITION'
maas $PROFILE tag update $TAG_NAME definition=''
maas $PROFILE tag update-nodes $TAG_NAME add=$SYSTEM_ID
```

<!-- LINKS -->