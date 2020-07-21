The [web UI](/t/commissioning-and-hardware-testing-scripts/833) enables you to easily write, upload and execute your hardware testing scripts and see the results. However, the [MAAS CLI](/t/maas-cli/802) supports options not available from the web UI, including access to earlier versions of your scripts, script deletion and result automation.

#### Quick questions you may have:

* [How do I upload and manage test scripts?](/t/cli-commissioning-and-hardware-testing-scripts/832#heading--script-management)
* [How do I use tags to help with test scripts?](/t/cli-commissioning-and-hardware-testing-scripts/832#heading--tags)
* [How do I view test results?](/t/cli-commissioning-and-hardware-testing-scripts/832#heading--results)
* [What metadata fields are available to me for test scripts?](/t/commissioning-and-hardware-testing-scripts/833#heading--metadata-fields)

<h2 id="heading--script-management">Script management</h2>

To upload a hardware testing script to MAAS, enter the following:

``` bash
maas $PROFILE node-scripts create name=$SCRIPT_NAME name> \
 script=$PATH_TO_SCRIPT type=testing
```

Changing the type to commissioning adds the test script to the commissioning process.

You can list all uploaded scripts with the following command:

``` bash
maas $PROFILE node-scripts read type=testing filters=$TAG
```

The optional filters argument lets you search for tags assigned to a script, such as using `TAG=cpu` with the above example.

A [script's metadata](/t/commissioning-and-hardware-testing-scripts/833#hardware-test-script-sample), and even the script itself, can be updated from the command line:

``` bash
maas $PROFILE node-script update \
 $SCRIPT_NAME script=$PATH_TO_SCRIPT comment=$COMMENT
```

The JSON formatted output to the above command will include 'history' dictionary entries, detailing script modification times and associated comments:

``` json
"history": [
    {
        "id": 40,
        "created": "Tue, 12 Sep 2017 12:12:08 -0000",
        "comment": "Updated version"
    },
    {
        "id": 34,
        "created": "Fri, 08 Sep 2017 17:07:46 -0000",
        "comment": null
    }
]
```

MAAS keeps a history of all uploaded script versions, allowing you to easily revert to a previous version using the `id` of the version you wish to revert to:

``` bash
maas $PROFILE node-script revert $SCRIPT_NAME to=$VERSION_ID
```

[note type="negative" status="Warning"]
The history for later modifications will be lost when reverting to an earlier version of the script.
[/note]

To download a script, enter the following:

``` bash
maas $PROFILE node-script download $SCRIPT_NAME > $LOCAL_FILENAME
```

To delete a script, use `delete`:

``` bash
maas $PROFILE node-script delete $SCRIPT_NAME
```

<h2 id="heading--tags">Tags</h2>

As with general [tag management](/t/cli-tag-management/801), tags make scripts easier to manage; grouping scripts together for commissioning and testing, for example:

``` bash
maas $PROFILE node-script add-tag $SCRIPT_NAME tag=$TAG
maas $PROFILE node-script remove-tag $SCRIPT_NAME tag=$TAG
```

MAAS runs all commissioning scripts by default. However, you can select which custom scripts to run during commissioning by name or tag:

``` bash
maas $PROFILE machine commission \
 commissioning_scripts=$SCRIPT_NAME,$SCRIPT_TAG
```

You can also select which testing scripts to run by name or tag:

``` bash
maas $PROFILE machine commission \
 testing_scripts=$SCRIPT_NAME,$SCRIPT_TAG
```

Any testing scripts tagged with commissioning will also run during commissioning.

<h2 id="heading--results">Results</h2>

The command line allows you to not only view the current script's progress but also retrieve the verbatim output from any previous runs too.

If you only want to see the latest or currently-running result, you can use `current-commissioning`, `current-testing`, or `current-installation` instead of an id:

``` bash
maas $PROFILE node-script-result read $SYSTEM_ID $RESULTS
```

You can also limit which results are returned by type (commissioning, testing, or installation), script name, or script run:

``` bash
maas $PROFILE node-script-results read \
 $SYSTEM_ID type=$SCRIPT_TYPE filters=$SCRIPT_NAME,$TAGS
```

You can also suppress failed results, which is useful if you want to ignore a known failure:

``` bash
maas $PROFILE node-script-results update \
 $SYSTEM_ID type=$SCRIPT_TYPE filters=$SCRIPT_NAME,$TAGS suppressed=$SUPPRESSED
```

where `$SUPPRESSED` is either `True` or `False`. The JSON formatted output to the above command will include 'results' dictionary with an entry for `suppressed`:

``` json
"results": [
    {
        "id": 21,
        "created": "Tue, 02 Apr 2019 17:00:36 -0000",
        "updated": "Tue, 02 Apr 2019 20:56:41 -0000",
        "name": "smartctl-validate",
        "status": 5,
        "status_name": "Aborted",
        "exit_status": null,
        "started": "Tue, 02 Apr 2019 20:56:41 -0000",
        "ended": "Tue, 02 Apr 2019 20:56:41 -0000",
        "runtime": "0:00:00",
        "starttime": 1554238601.765214,
        "endtime": 1554238601.765214,
        "estimated_runtime": "0:00:00",
        "parameters": {
            "storage": {
                "argument_format": "{path}",
                "type": "storage",
                "value": {
                    "id_path": "/dev/vda",
                    "model": "",
                    "name": "sda",
                    "physical_blockdevice_id": 1,
                    "serial": ""
                }
            }
        },
        "script_id": 1,
        "script_revision_id": null,
        "suppressed": true
    }
]
```

Finally, results can be downloaded, either to stdout, stderr, as combined output or as a tar.xz:

``` bash
maas $PROFILE node-script-result download $SYSTEM_ID $RUN_ID output=all \
 filetype=tar.xz > $LOCAL_FILENAME
```

[note]
**$RUN_ID** is labelled `id` in the verbose result output.
[/note]

<!-- LINKS -->