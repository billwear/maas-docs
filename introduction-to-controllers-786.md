Most of the functionality of MAAS is contained in a series of controllers.  There are two basic types: a region controller and one or more rack controllers. The region controller deals with operator requests, while the rack controller(s) provides high-bandwidth services to the individual machines.  In essence, the region controller interacts with the user, while the rack controllers manage the bare metal.   Note that both region and rack controllers can be scaled out, as well as made [highly available](/t/high-availability/804).

#### Quick questions you might have:

* [What does a region controller do?](/t/introduction-to-controllers/786#heading--region-controller)
* [What does a rack controller do?](/t/introduction-to-controllers/786#heading--rack-controllers)

<h2 id="heading--region-controller">What a region controller does</h2>

A region controller consists of:

-   REST API server (TCP port 5240)
-   PostgreSQL database
-   DNS
-   caching HTTP proxy
-   web UI

Region controllers are responsible for either a data centre or a single region. Multiple fabrics are used by MAAS to accommodate subdivisions within a single region, such as multiple floors in a data centre.

<h2 id="heading--rack-controllers">What a rack controller does</h2>

A rack controller provides:

-   DHCP
-   TFTP
-   HTTP (for images)
-   power management

A rack controller is attached to each "fabric". As the name implies, a typical setup is to have a rack controller in each data centre server rack. The rack controller will cache large items for performance, such as operating system install images, but maintains no independent state other than the credentials required to talk to the region controller.

<details><summary>Tell me about fabrics</summary>

A fabric is simply a way of linking [VLANs](/t/concepts-and-terms/785#heading--vlans) (Virtual LANs) together.  If you're familiar with a VLAN, you know that it's designed to limit network traffic to specific ports (e.g., on a [switch](/t/concepts-and-terms/785#heading--switch)) or by evaluating labels called "tags" (unrelated to MAAS tags).  By definition, this would mean that two VLANs can't communicate with each other -- it would defeat the purpose of the VLAN -- unless you implement some extraordinary measures.

For example, let's say that your [hospital](https://maas.io/docs/maas-example-config) has three key functions: Patient management, Accounting, and Facilities, each on their own VLAN.  Let's say that there are some situations in which you need to share data between all three of these functions.  To accomplish this, you can create a fabric that joins these three VLANS.  Since this fabric just makes it possible for these VLANs to communicate, you can manage the cross-VLAN access with additional software, or permissions, depending on your application software architecture.

You can learn more about fabrics in the [Concepts and terms](/t/concepts-and-terms/785#heading--fabrics) section of this documentation.