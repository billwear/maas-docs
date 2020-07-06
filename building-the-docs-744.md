[note type="negative" status="Deprecated page"]
This article and the processes described herein have been deprecated. You can now make or suggest edits to the documentation by choosing the "Help improve this document in the forum" link, at the bottom of each documentation page.  When editing, please follow the guidelines in [the writing guide](https://maas.io/docs/writing-guide).  Please make a note of this.
[/note]
<!--
Todo:
- can add more user-friendly instructions on setting up local web server
- talk about entr utility
-->
Every non-trivial contribution must first have its HTML built and verified before a pull request (PR) is made from it.

See the [documentation-builder project](https://github.com/CanonicalLtd/documentation-builder) for details of the actual tool.

Related documents:

-   [Writing guide](/t/writing-guide/747)
-   [Working with Git and GitHub](/t/working-with-git-and-github/746)

<h2 id="heading--installation">Installation</h2>

Install the builder. On Ubuntu 16.04 LTS:

``` bash
sudo snap install documentation-builder
```

[note]
You will first need to install package `squashfuse` if you're doing this in a LXD container.
[/note]

To build the HTML, while in the root of the MAAS docs repository:

``` bash
documentation-builder
```

<h2 id="heading--verification">Verification</h2>

You can point a web browser at individual HTML files but to make your verification more conclusive you will need a web server.

<h3 id="heading--web-server">Web server</h3>

See the [Ubuntu Server Guide](https://help.ubuntu.com/lts/serverguide/httpd.html) for instructions on setting up Apache. The DocumentRoot should be the `build` directory. To test, point your browser at:

``` no-highlight
http://127.0.0.1/en/contributing.html
```

Alternatively, you can use Python to start a simple HTTP server (port 8000). While in the `build` directory run:

``` bash
python -m SimpleHTTPServer
```

With Python 3:

``` bash
python3 -m http.server
```

<h3 id="heading--points-to-consider">Points to consider</h3>

Some things to consider during verification:

-   A linkchecker (either a system-wide tool or a [browser add-on](https://chrome.google.com/webstore/detail/check-my-links/ojkcdipcgfaekbeaelaapakgnjflfglf))
-   Images should show enough context (surrounding real estate) but not so much to make important details illegible.

<!-- LINKS -->