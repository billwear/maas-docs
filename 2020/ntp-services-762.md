MAAS provides managed NTP services (with [Chrony](https://chrony.tuxfamily.org/)) for all region and rack controllers. This arrangement allows MAAS to both keep its controllers synchronised, and keep deployed machines synchronised as well. You can configure NTP on the 'Network services' tab of the 'Settings' page.

The region controller configures the NTP service to keep its time synchronised from one or more external sources. By default, the MAAS region controller uses `ntp.ubuntu.com`. Rack controllers also configure the NTP service, synchronising their time with the region controllers.  Rack controllers also configure DHCP with the correct NTP information. Any machine on the network that obtains a DHCP lease from MAAS will benefit from NTP support.

<h2 id="heading--setting-ntp-server">Setting an external NTP server</h2>

External sites, such as an existing NTP infrastructure, can be used directly as a time source for both rack controllers and machines. You can specify an external site by choosing the NTP server(s) and selecting the 'External Only' option. The region controller always uses an external site.

On the 'Settings' page, select the 'Network services' tab and scroll down to the 'NTP' section:

![configure NTP](https://assets.ubuntu.com/v1/69180b4a-installconfig-network-ntp__2.6-configure-ntp.png)

Enter the address of the desired NTP server. Apply any changes by pressing the 'Save' button.

<!-- LINKS -->