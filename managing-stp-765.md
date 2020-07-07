Some switches use the Spanning-Tree Protocol (STP) to negotiate a loop-free path through a root bridge. While scanning, it can cause each port to wait up to 50 seconds before sending data. This delay, in turn, can cause problems with some applications/protocols such as PXE, DHCP and DNS, of which MAAS makes extensive use.

To alleviate this problem, you should enable [Portfast](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst4000/8-2glx/configuration/guide/stp_enha.html#wp1019873) for Cisco switches or its equivalent on other vendor equipment, which allows the ports to come up almost immediately.

<!-- LINKS -->