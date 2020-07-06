MAAS services can provide [Prometheus](https://prometheus.io/) endpoints for collecting performance metrics.  These include:

-   TFTP server file transfer latency
-   HTTP requests latency
-   Websocket requests latency
-   RPC calls (between MAAS services) latency
-   Per request DB queries counts

All available metrics are prefixed with `maas_`, to make it easier to look them up in Prometheus and Grafana UIs.

#### Quick questions you may have:

* [How do I enable Prometheus endpoints?](/t/prometheus-metrics/813#heading--enabling-prometheus-endpoints)
* [How do I configure Prometheus endpoints?](/t/prometheus-metrics/813#heading--configuring-prometheus)
* [How can I deploy Prometheus and Grafana?](/t/prometheus-metrics/813#heading--deploying-prometheus-and-grafana)

<h2 id="heading--enabling-prometheus-endpoints">Enabling Prometheus endpoints</h2>

Whenever you install the `python3-prometheus-client` library, Prometheus endpoints are exposed over HTTP by the `rackd` and `regiond` processes under the default `/metrics` path.

For a snap-based MAAS installation, the libraries already included in the snap so that metrics will be available out of the box.

For a Debian-based MAAS installation, install the library and restart MAAS services as follows:

    sudo apt install python3-prometheus-client
    sudo systemctl restart maas-rackd
    sudo systemctl restart maas-regiond

MAAS also provides optional stats about resources registered with the MAAS server itself.

These include:

-   The number of nodes by type, arch, ...
-   Number of networks, spaces, fabrics, VLANs and subnets
-   Total counts for machines CPU cores, memory and storage
-   Counters for VM host resources

After installing the `python3-prometheus-client` library as describe above, run the following to enable stats:

    maas $PROFILE maas set-config name=prometheus_enabled value=true

<h2 id="heading--configuring-prometheus">Configuring Prometheus</h2>

Once the `/metrics` endpoint is available in MAAS services, Prometheus can be configured to scrape metric values from these. You can configure this by adding a stanza like the following to the [prometheus configuration](https://prometheus.io/docs/prometheus/latest/configuration/configuration/):

``` yaml
    - job_name: maas
      static_configs:
        - targets:
          - <maas-host1-IP>:5239  # for regiond
          - <maas-host1-IP>:5249  # for rackd
          - <maas-host2-IP>:5239  # regiond-only
          - <maas-host3-IP>:5249  # rackd-only
```

If the MAAS installation includes multiple nodes, the `targets` entries must be adjusted accordingly, to match services deployed on each node.

If  you have enabled MAAS stats,  you must add an additional Prometheus job to the config:

``` yaml
    - job_name: maas
      metrics_path: /MAAS/metrics
      static_configs:
        - targets:
          - <maas-host-IP>:5240
```

In case of a multi-host deploy, adding a single IP for any of the MAAS hosts running `regiond` will suffice.

<h2 id="heading--deploying-prometheus-and-grafana">Deploying Prometheus and Grafana</h2>

[Grafana](https://grafana.com/) and Prometheus can be easily deployed using [Juju](https://jujucharms.com/).

The [MAAS performance repo](https://git.launchpad.net/~maas-committers/maas/+git/maas-performance) repository provides a sample `deploy-stack` script that will deploy and configure the stack on LXD containers.

First, you must install juju via:

    sudo snap install --classic juju

Then you can run the script from the repo:

    grafana/deploy-stack <MAAS-IP>

To follow the progress of the deployment, run the following:

    watch -c juju status --color

Once you deploy everything, the Grafana UI is accessible on port `3000` with the credentials `admin`/`grafana`. The Prometheus UI will be available on port `9090`.

The repository also provides some sample dashboard covering the most common use cases for graphs. These are available under `grafana/dashboards`.  You can import them from the Grafana UI or API.

<!-- LINKS -->