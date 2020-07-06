A rack controller can connect to multiple VLANs, each from a different network interface. This configuration provides a scaling factor that can help as a network architecture grows in size.

#### Quick questions you may have:

* [How does MAAS communication work?](/t/maas-communication/783)
* [How do I install a rack controller?](/t/rack-controllers/771#heading--install-a-rack-controller)
* [How do I configure MAAS for multiple API servers?](/t/high-availability/804#heading--multiple-region-endpoints)
* [How do I unregister a rack controller?](/t/rack-controllers/771#heading--unregister-a-rack-controller)

<h2 id="heading--install-a-rack-controller">Install a rack controller</h2>

Installing a rack controller consists of installing the rack controller software and then registering the rack controller with the region API server.  When MAAS installs a rack controller on the same host as the region API server, registration occurs automatically.  Rack controllers can also be [added and registered manually](/t/cli-advanced-tasks/793#heading--install-a-rack-controller).

Once registered, this extra rack controller will appear immediately alongside the primary controller in the web UI and begin to sync with it:

![4_install-rackd|690x251](upload://1tKysphOEUVzsDrcvNegIO4Aanr.png) 

You can also [list and confirm](/t/cli-advanced-tasks/793#heading--list-rack-controllers) all registered rack controllers via the CLI.  Note that you will need multiple rack controllers to achieve specific [high availability](/t/high-availability/804) configurations.

<div class="p-notification">
<p class="p-notification__response">If you are using VM nodes, you must ensure that the new rack controller can communicate with the VM host.</p>
</div>

<h2 id="heading--unregister-a-rack-controller">Unregister a rack controller</h2>

Most likely, you would only “unregister” an extra, unnecessary rack controller.  In this case, you need to <em>delete</em> it from the region API server; there is no ‘unregister’ command.

To do so, navigate to the ‘Controllers’ page of the web UI. Enter the controller’s page by clicking on the machine you want to delete and select ‘Delete’ from the dropdown (and then ‘Delete controller’). MAAS will do the right thing if the controller is used for DHCP HA; that is, the DHCP HA needs to be disabled.

Although similar, this is not the same as deletion. Here, you are deleting a machine that is a part of MAAS itself.

<div class="p-notification">
<p class="p-notification__response">Unless you remove the software on this machine, rebooting it will cause the machine to re-instate itself as a rack controller. This behaviour may change with future versions of MAAS.</p>
</div>