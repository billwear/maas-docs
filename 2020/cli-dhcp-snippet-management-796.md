This document presents a list of DHCP snippet management tasks that you can perform with the MAAS CLI.

#### Quick questions you may have:

* [How do I get started with the MAAS CLI?](/t/maas-cli/802)
* [What are DHCP snippets and how do they work?](/t/managing-dhcp/759#heading--dhcp-snippets)
* [How do I create a DHCP snippet?](/t/cli-dhcp-snippet-management/796#heading--create-a-snippet)
* [How do I list DHCP snippets?](/t/cli-dhcp-snippet-management/796#heading--list-snippets)
* [How do I update a DHCP snippet?](/t/cli-dhcp-snippet-management/796#heading--update-a-snippet)
* [How do I enable or disable a DHCP snippet?](/t/cli-dhcp-snippet-management/796#heading--enable-or-disable-a-snippet)
* [How do I delete a DHCP snippet?](/t/cli-dhcp-snippet-management/796#heading--delete--a-snippet)

<h2 id="heading--create-a-snippet">Create a snippet</h2>

When you create a snippet, MAAS enables it by default.

To create a **global** snippet:

``` bash
maas $PROFILE dhcpsnippets create name=$DHCP_SNIPPET_NAME \
    value=$DHCP_CONFIG description=$DHCP_SNIPPET_DESCRIPTION \
    global_snippet=true
```

To create a **subnet** snippet:

``` bash
maas $PROFILE dhcpsnippets create name=$DHCP_SNIPPET_NAME \
    value=$DHCP_CONFIG description=$DHCP_SNIPPET_DESCRIPTION \
    subnet=$SUBNET_ID
```

You can also specify subnets in CIDR format.

To create a **node** snippet:

``` bash
maas $PROFILE dhcpsnippets create name=$DHCP_SNIPPET_NAME \
    value=$DHCP_CONFIG description=$DHCP_SNIPPET_DESCRIPTION \
    node=$NODE_ID
```

You can also use a hostname instead of the node ID.

<h2 id="heading--list-snippets">List snippets</h2>

To list all snippets (and their characteristics) in the MAAS:

``` bash
maas $PROFILE dhcpsnippets read
```

To list a specific snippet:

``` bash
maas $PROFILE dhcpsnippet read id=$DHCP_SNIPPET_ID
```

The snippet name can also be used instead of its ID:

``` bash
maas $PROFILE dhcpsnippet read name=$DHCP_SNIPPET_NAME
```

<h2 id="heading--update-a-snippet">Update a snippet</h2>

Update a snippet attribute:

``` bash
maas $PROFILE dhcpsnippet update $DHCP_SNIPPET_ID <option=value>
```

You can also use a snippet name instead of its ID.

<h2 id="heading--enable-or-disable-a-snippet">Enable or disable a snippet</h2>

Enabling and disabling a snippet is considered a snippet update and is done via a boolean option ('true' or 'false'). You can disable a snippet like this:

``` bash
maas $PROFILE dhcpsnippet update $DHCP_SNIPPET_ID enabled=false
```

When you disable a snippet, MAAS removes the text you added to the dhcpd.conf file when you created the snippet.

<h2 id="heading--delete-a-snippet">Delete a snippet</h2>

To delete a snippet:

``` bash
maas $PROFILE dhcpsnippet delete $DHCP_SNIPPET_ID
```

You can also use a snippet name in place of its ID.

<!-- LINKS -->