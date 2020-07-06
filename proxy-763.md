MAAS provides a way for its managed machines to use a proxy server when they need to access HTTP/HTTPS-based resources, such as the Ubuntu package archive.

There are three possible options:

-   internal proxy (default)
-   external proxy
-   no proxy

Configuring proxying with MAAS consists of enabling/disabling one of the above three options and enabling/disabling proxying on a specific subnet.

#### Quick questions you may have:

* [How and why should I create an internal proxy?](/t/proxy/763#heading--internal-proxy-maas-proxy)
* [How and why should I create an external proxy?](/t/proxy/763#heading--configure-proxy)

<h2 id="heading--internal-proxy-maas-proxy">Internal proxy (MAAS proxy)</h2>

MAAS provides an internal proxy server. Although it is set up to work well with APT/package requests, it is effectively an HTTP caching proxy server. If you configure the MAAS region controller as the default gateway for the machines it manages then the proxy will work transparently (on TCP port 3128). Otherwise, machines will need to access it on TCP port 8000.

By default, the proxy is available to all hosts residing in any subnet detected by MAAS, not just MAAS-managed machines. It is therefore recommended to disable access to those subnets that represent untrusted networks.

MAAS manages its proxy. So although the active configuration, located in file `/var/lib/maas/maas-proxy.conf`, can be inspected, it is not to be hand-edited.

You must install the proxy on the same host as the region controller (via the 'maas-proxy' package).

<h2 id="heading--configure-proxy">Configure proxy</h2>

See the [MAAS CLI](/t/common-ou cli-tasks/794#heading--configure-proxying) for how to configure proxying with the CLI. Note that you can only configure per-subnet proxies via the CLI.

In the web UI, visit the 'Settings' page and select the 'Network services' tab. The 'Proxy' section is at the top. You can apply your changes by pressing the 'Save' button.

![Configure proxy](https://assets.ubuntu.com/v1/55800a33-installconfig-network-proxy__2.4_configure-proxy.png)

To enable the internal proxy, ensure that the checkbox adjacent to 'MAAS Built-in' is selected. This internal proxy is the default configuration.

To enable an external proxy, activate the 'External' checkbox and use the new field that is displayed to define the proxy's URL (and port if necessary).

An upstream cache peer can be defined by enabling the 'Peer' checkbox and entering the external proxy URL into the field. With this enabled, machines will be configured to use the MAAS built-in proxy to download cached APT packages.

To prevent MAAS machines from using a proxy, enable the 'Don't use a proxy' checkbox.

[note type="caution"]Note that the proxy service will still be running[/note]

<!-- LINKS -->