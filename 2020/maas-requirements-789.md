The minimum requirements for the machines that run MAAS vary widely depending on local implementation and usage.  Below, you will find resource estimates based on MAAS components and operating system (Ubuntu Server). We consider both a test configuration (for proof of concept) and a production environment.

#### Quick questions you might have:

* [What are the requirements for a test environment?](/t/maas-requirements/789#heading--test-environment)
* [What are the requirements for a production environment?](/t/maas-requirements/789#heading--production-environment)

<h2 id="heading--test-environment">Requirements for a test environment</h2>

Here is a proof-of-concept scenario, with all MAAS components installed on a single host. This scenario assumes two complete sets of images (latest two Ubuntu LTS releases) for a single architecture (amd64).

<div class="md-table">
<table>
<thead>
<tr>
<th></th>
<th>Memory (MB)</th>
<th>CPU (GHz)</th>
<th>Disk (GB)</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<a href="https://maas.io/docs/concepts-and-terms#heading--controllers">Region controller</a> (minus PostgreSQL)</td>
<td>512</td>
<td>0.5</td>
<td>5</td>
</tr>
<tr>
<td>PostgreSQL</td>
<td>512</td>
<td>0.5</td>
<td>5</td>
</tr>
<tr>
<td><a href="https://maas.io/docs/concepts-and-terms#heading--controllers">Rack controller</a></td>
<td>512</td>
<td>0.5</td>
<td>5</td>
</tr>
<tr>
<td>Ubuntu Server (including logs)</td>
<td>512</td>
<td>0.5</td>
<td>5</td>
</tr>
</tbody>
</table>
</div>

Based on this table, the approximate requirements for this scenario are 2 GB memory, 2 GHz CPU, and 20 GB of disk space.

<h2 id="heading--production-environment">Requirements for a production environment</h2>

Here is a production scenario designed to handle a high number of sustained client connections. This scenario implements both high availability (region and rack) and load balancing (region). MAAS reserves extra space for images (database and rack controller), while some images, such as those for Microsoft Windows, may require a lot more -- so plan accordingly.

<div class="md-table">
<table>
<thead>
<tr>
<th></th>
<th>Memory (MB)</th>
<th>CPU (GHz)</th>
<th>Disk (GB)</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<a href="https://maas.io/docs/concepts-and-terms#heading--controllers">Region controller</a> (minus PostgreSQL)</td>
<td>2048</td>
<td>2.0</td>
<td>5</td>
</tr>
<tr>
<td>PostgreSQL</td>
<td>2048</td>
<td>2.0</td>
<td>20</td>
</tr>
<tr>
<td><a href="https://maas.io/docs/concepts-and-terms#heading--controllers">Rack controller</a></td>
<td>2048</td>
<td>2.0</td>
<td>20</td>
</tr>
<tr>
<td>Ubuntu Server (including logs)</td>
<td>512</td>
<td>0.5</td>
<td>20</td>
</tr>
</tbody>
</table>
</div>

So, based on the above, the approximate requirements for this scenario are:

* A region controller (including PostgreSQL) installed on one host, with 4.5 GB memory, 4.5 GHz CPU, and 45 GB of disk space.
* A duplicate region controller (including PostgreSQL) on a second host, also with 4.5 GB memory, 4.5 GHz CPU, and 45 GB of disk space.
* A rack controller installed on a third host, with 2.5 GB memory, 2.5 GHz CPU, and 40 GB of disk space.
* A duplicate rack controller on a fourth host, also with 2.5 GB memory, 2.5 GHz CPU, and 40 GB of disk space.

[note]
The tables above refer to MAAS infrastructure only. They do not cover the resources needed by subsequently-added nodes. Note that machines should have IPMI-based BMC controllers for power cycling, see [Power management](/t/power-management/830) for more details.
[/note]

Some examples of factors that influence hardware specifications include:

* the number of connecting clients (client activity)
* how you decide to distribute services
* whether or not you use [high availability/load balancing](/t/high-availability/804).
* the number of images that you choose to store (disk space affecting PostgreSQL and the rack controller)

Also, this discussion does not take into account a possible local image mirror, which would be a large consumer of disk space.

One rack controller should only service 1000 machines or less, regardless of how you distribute them across subnets. There is no load balancing at the rack level, so you will need additional, independent rack controllers. Each controller must service its own subnet(s).