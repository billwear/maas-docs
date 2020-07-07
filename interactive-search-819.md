The Machines and Devices pages contain a powerful interactive search bar that lets you filter machines and devices.  This article is designed to help you understand how to use this resource.

---

Quick questions you may have:

* [How do I use the Web UI?](/t/web-ui/782)
* [How can I do simple searches?](/t/interactive-search/819#heading--simple-searches)
* [How can I do filtered searches?](/t/interactive-search/819#heading--filtered-searches)
* [How can I create manual filters?](/t/interactive-search/819#heading--manual-filters)
* [How do I set up exact matching?](/t/interactive-search/819#heading--exact-matching)
* [How do I set up partial matching?](/t/interactive-search/819#heading--partial-matching)
* [How can I include multiple search terms?](/t/interactive-search/819#heading--multiple-search-terms)
* [What filter properties are available to me?](/t/interactive-search/819#heading--filter-properties)
* [How can I search with the CLI?](/t/interactive-search/819#heading--cli)

---

<h2 id="heading--simple-searches">Simple searches</h2>

To begin searching, enter your search text. As you type, MAAS will update search results in real time. You can search across virtually every parameter, including domain, name, tag, power type, IP, status, zone, and so on.

![searchbar](https://assets.ubuntu.com/v1/ccb90c91-manage-search__2.5_searchbar.png)

<h2 id="heading--filtered-searches">Filtered searches</h2>

Filter your searches to major categories by selecting one from the Filters dropdown menu next to the search bar.

![filters](https://assets.ubuntu.com/v1/6ac5b4ec-manage-search__2.5_filters.png)

For example, to find all "New" machines containing the name or tag 'quail', select Status/New from the dropdown Filters menu, and then type the word 'quail' next to it:

![filterresult](https://assets.ubuntu.com/v1/7b5d8e86-manage-search__2.5_filtered-search.png)

<h3 id="heading--manual-filters">Manual filters</h3>

Enter filters manually in the search bar to more precisely control your searches:

``` no-highlight
filter-name:([=]val1,...,[=]val2)
```

!!! Note:

    Surround search terms with parentheses when they must occur together,
    E.g.  `status:(failed testing)`.

<h4 id="heading--exact-matching">Exact matching</h4>

If you need an exact match, preface the search value with an equal sign. For example, to find machines belonging to a pod named `able-cattle`:

``` no-highlight
pod:=able-cattle
```

<h4 id="heading--partial-matching">Partial matching</h4>

Without an equal sign, MAAS returns partial matches. For example, the following will display all machines belonging to pods with names containing `able` or `cattle`:

``` no-highlight
pod:able,cattle
```

<h4 id="heading--multiple-search-terms">Multiple search terms</h4>

MAAS uses boolean AND logic to evaluate multiple search terms. For example, when you type `pod:able,cattle cpu:=5`, MAAS displays machines that belong to pods with names containing `able` OR `cattle` AND having 5 CPU cores. Similarly, if you enter multiple words into the search tool, e.g., `steady able`, MAAS will display machines matching both terms (e.g., `steady` AND `able`).

<h4 id="heading--filter-properties">Filter properties</h4>

In addition to the major filtering categories available in the Filter dropdown menu, the following machine properties are available as filters:

-   architecture
-   cores
-   cpu
-   distro_series
-   fqdn
-   ip_addresses
-   mac
-   pod
-   pod-id
-   pool
-   power
-   pxe_mac
-   pxe_mac_vendor
-   ram
-   release
-   spaces
-   status
-   storage
-   storage_tags
-   tags
-   zone

For example, the following will search for a machine that has an interface with a specific MAC address marked for PXE booting:

``` no-highlight
pxe_mac:=9e:b0:e4:15:ae:1e
```

<h2 id="heading--cli">CLI</h2>

See [List nodes](/t/common-cli-tasks/794#heading--list-nodes) for more information about how to use the MAAS CLI to perform similar searches or [CLI](/t/maas-cli/802) for how to get started with the MAAS CLI.

<!-- LINKS -->