Add an RSD host by using the 'Add pod' button. Choose 'Rack Scale Design' from the pod type drop-down menu.

![add RSD pod](https://assets.ubuntu.com/v1/2604a48b-nodes-comp-hw__2.4_pod-add-rsd.png)

You will need to get values for 'Pod address' (IP address or URL followed by a port), 'Pod user', and 'Pod password' from your RSD administrator.

[note]
MAAS will automatically discover and store the resources your RSD Pod contains.
[/note]

<h2 id="heading--cli">CLI</h2>

``` bash
maas $PROFILE pods create type=rsd power_address=10.3.0.1:8443 \
    power_user=admin power_pass=admin
```

<!-- LINKS -->