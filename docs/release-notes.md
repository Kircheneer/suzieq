# Release Notes

## 0.11 (Apr 6, 2021)

This is a mostly a repackaged 0.10 release, but now shippable as a python package as well. You can use pip install to install Suzieq. As a consequence, some of the directory structures (for config) have changed, and the suzieq config file format has been streamlined and made consistent so the variables appropriate to each component is present in the right hierarchy. The older config file will be subsumed and converted internally.

## 0.10 (Apr 1, 2021)

No, it isn't an April Fools release. There are several major features in this release. 

* **Platforms**: __Added support for IOSXR and Juniper SRX platforms__. EOS now supports VXLAN info (evpnVni table) and has support for SSH and REST. NXOS performance improved to use [json native instead of json]( https://www.cisco.com/c/en/us/td/docs/dcn/nx-os/nexus9000/101x/programmability/cisco-nexus-9000-series-nx-os-programmability-guide-release-101x/m-n9k-nx-api-cli-101x.html). You can see the supported platforms and tables [here](https://suzieq.readthedocs.io/en/latest/tables/).
* **Support for augmented columns**: Augmented columns are columns that are computed based on other columns, typically from the same table, but possibly from other tables as well. Examples of augmented columns includes peerHostname in case of OSPF, moveCount in MAC tables and so on. Details about augmented columns and their implementation can be found in this [doc](https://github.com/netenglabs/suzieq/blob/master/docs/developer/augmented-columns.md). From the user's perspective, an augmented column is indistinguishable from any other column. The benefit of augmented columns is that with the introduction as a column, different queries such as unique, finding frequency distribution etc. become natural.
* __Support for generalized query__. We store many key fields for each table. BGP stores 50+ different fields per session. Providing an individual filter per field is too cumbersome. In addition, there are fairly more combining of fields to create a more powerful filter that is hard to do with individual key/value filter fields. So, we added support for a generalized filter field where you can express complex filter fields via a generaliized pandas query format. This [link](https://suzieq.readthedocs.io/en/latest/pandas-query-examples/) provides samples for some of the more common queries. Ask for help on the Slack channel if you're stumped.
* **Support for providing a password via an ENVIRONMENT variable**. Today, we support providing password via either a prompt on launching the poller or in the inventory file. This release adds support for reading the password from a user specified environment variable. Using the --envpass=<ENVVAR> when starting the sq-poller allows you to use this feature. 
* **Support for mac move**. You can now get a field called moveCount in the MAC table that allows you to see how many times a MAC has moved. You can filter MAC entries that have moved more than X times in a period, see all the different moves of a MAC (outgoing interface or remote VTEP change) in a given period, see frequency distribution of how the moves are (100 MACs moved 20 times, 5 MACs moved 10 times etc.)
* **Support for multiple address families in BGP**. In the initial releases, BGP table only supported looking up IPv4/IPv6 unicast and EVPN AFI/SAFI info. This version adds support for pretty much all the BGP AFI/SAFI. We normalize the AFI/SAFI names across all the NOS so you don't have to know every NOS' version of it. We've hopefully picked names that are sensible to all users. Please provide us feedback about this if the names are an issue for you. 
* Improved summarizations for BGP and a few other tables.
* Improved syntax checking of inventory file.
* Qualify search results in GUI with namespace.
* Tons of bug fixes in parsing info, analyzing info, path trace, coalescing and more.
* We've added a lot more tests.

## 0.9 (Feb 17, 2021)

The main feature of this release is the support for more efficient storage and providing fast, almost constant query times independent of the time window you're searching for (5 mins ago OR 5 mins a year back). There are a few other minor bug fixes. There's a new process called sq-coalescer that's running in the background. Its launched automatically by sq-poller. 

* Juniper commands now have "no-more" added to the end to prevent some older machines from blocking (Issue #314)
* device now has serial number and OS name (nxos, eos, cumulus etc.). This is in preparation for support of IOS-XR and IOS-XE.
* bgp show view=all was broken (Issue #318)
* Graceful termination of sq-poller on Ctrl-C or SIGINT (Issue #321)

## 0.8.7 (Jan 11, 2020)

A hotfix release to ensure REST server works. 0.8.6. broke the REST interface. 

## 0.8.6 (Jan 11, 2020)

This is another quick release to make path work well in more conditions including support for VXLAN L2 trace with EOS, reverse path check, handling NXOS vagaries like having interface names such as "vPC Peer Link" instead of the real peerlink interface name. We also added both in and out MTU to the output so that users can spot the issue in the path output itself.

The one additional feature we added was to allow users to specify the location and name of the SSL ceriticate and key files needed for the REST server. This feature was added thanks to Dennis Fanshaw working at a hyposcaler hyperbolic.

## 0.8.5 (Jan 4, 2020)

This is mainly improvements to path tracing, focused on debuging any issues with forwarding along the path. Visually, the way you can use path trace is far more interactive to troubleshoot than the first version. In the process of doing this, the release also found a bunch of data gathering issues with different NOS and fixed them, including capturing MTU on NXOS switched ports, fixing the mac table entries for Linux bridge so that the VLAN was properly derived for remote macs, getting the MAC address for Junos IRB interfaces and so on.

## 0.8 (Dec 14, 2020)

The three main features of the 0.8 release are the availability of a GUI, and the simplified experience of time, and Junos EX support. Here are the more detailed release notes:

* GUI support
* Simplified support of time.  You can specify time in English when querying by time. For example, '10 pm last night', or 'two days ago' or 'Jun 19' or 'two years ago'.
* Allow the user to provide the password for device login via a prompt rather than storing it in a file (--ask-pass option)
* Junos EX support. We've tested version 20.3R1.8.
* Display the admin state of an interface in interface show
* Fixed some bugs in parsing Junos EVPN information as well as interfaces
* Support for pandas query string in GUI and REST API. The CLI support for this will be in the next release
* Removed inconsistencies in storing interface oper state ('up', 'Up' were both present, for example).
* Updated the dependency libraries to newer versions.

Junos EX support is thanks to Andrea Florio.

## 0.7 (Nov 25, 2020)

There have a been a bunch of improvements in data gathering and making them consistent across NOSes in this release, along with improvements such as better summarizations, improved interface assert etc.

- Pager support within the CLI for longer entries
- Support for partial command specification. int sh completes to interface show
- Support for EVPN routes for EOS
- Path enhanced to support tracing from first hop switch and support for trunking ports
- Improved interface assert: match MTU, VLAN set, PVID, speed, IP address
- Improved OSPF assert, ignore admin down interfaces
- Markdown format output in both CLI and REST
- Allow specification of default poll period in config file. Saves laborious process of adding period to every resource file
- Filter assert outputs to only show whats failed, only  whats passed or all
- Improved VLAN summarize
- Fixed major bug in constructing mac table keys across various NOS
- Make summarize work with hostname qualifier
- JunOS route data normalization in the presence of OSPF unnumbered and EVPN routes
- Improved EVPN VNI info for JunOS
- SSH authentication improvementss: Support for username/password keywords in inventory to handle values that may use URL reserved chars such as '.'. Eg, usename dinesh.dutt is now supported via this method
- Anycast gateway MAC fix for SVIs with NXOS and EOS
- Improved filtering support for numerical fields
- ARP/ND entry fixes for Cumulus versions greater than 4.0
- Making interface data consistent across all NOS, support for portchannel interfaces in NXOS
- Test improvement in making pandas dataframe compare more robust
- Added 100 more tests

## 0.6 (Nov 5, 2020)

The most critical feature in this release is the support for a RESTful API. 

* Support for NXOS VPC and Arista's MLAG features
* Support for VLAN resource across all NOS: Juniper, Arista, Cisco and Cumulus. Unfortunately, this meant we had to throw away the existing Cumulus support and redo it to bring it in line with the other NOS.
* Support for prefixlen in route unique
* Support for Markdown as an output format. This is especially useful for chatbot support.
* Internal: Added 2K+ automated tests as part of testing REST that actually are tests of the underlying code for consistency and correctness across all resources.
* Fixed some bugs in path show with EVPN
* pandas version bumped to 1.1.4
* Bug fixes in writing ARPND data for NXOS
* Bug fixes to ensure all interfaces are associated with the right VRF in EOS
* Security fix: Bump cryptography version from 2.9.2 to 3.2 to fix some security issues as recommended by Github's dependabot.
* Fixed multiple bugs in underlying code for consistency of values passed and supported filters.
* UX Change: All protocol state is now flagged with the "state" filter, not the "status" filter. It takes specific values associated with the protocol, and the CLI offers completions for each.

## 0.5 (Sep 22, 2020)

This is a release with some major structural changes, and support for SONIC NOS.

* Support for SONIC NOS, thanks largely to Senthil Ganesan from Dell, with some additional assistance from Andrea Florio.
* Improved performance based on modified partitioning, and improved algorithms:
  * Read/Write Parquet uses the new dataset API
  - Improved predicate pushdown support with new dataset API for improved IO efficiency
  * We use vectorization and itertuples to support full Internet routing table with good performance. Thanks to Donald Sharp for this.
  * Row group size of 100000 and the ZSTD compression for better compression and read/write performance
* Schema evolution support. Now you won't need to throw away data with newer Suzieq releases. 
* (Alpha) Migration tool to help with migrating old data to new format
* Improved SSH security support including support for jumphosts, passphrase with private key, ssh config support. Thanks to Steve Dodd(idahood) for his help with testing and requesting the features.
* Support for complex Ansible inventory to retrieve username/password etc. via the ansible-inventory command output
* (Beta) Support for anonymizing data. At this time, it anonymizes hostnames, IP addresses and MAC addresses. It anonymizes the data gathered via --run-once=gather option of sq-poller, not the parquet data itself.
* Improved support for JunOS timestamps that caused some unnecessary data gathering in various scenarios.
* Improved and consistent support for filters based on '<, >, <=, >=, !=' including supporting these for filters that weren't supported before such as VLANs and MAC addresses
* Bug fixes to path show to handle VRR interfaces (in Cumulus) correctly

## 0.4.1 (August 23, 2020)

This is a hotfix release to fix the following two critical bugs in the 0.4 release:

* [Issue 237](https://github.com/netenglabs/suzieq/issues/237). JunOS interface poller had a bug that caused it to continue dumping the interface information gathered every polling interval even if nothing has changed. This causes the interface database to increase unnecessarily.
* The show commands had their column display order jumbled up instead of a well-known, fixed order.

Besides this, there was another bug fixed which ensured that we didn't use the transient, unmassaged fields in the computation of the difference between the current poller result from the result of the previous poll.

## 0.4 (August 18, 2020)

In addition to bug fixes, the new features in this release are:

* Support for vastly improved path command. You can get overlay plus undelay path traced, support for outgoing interface as well as incoming interface, a bunch of bugs fixed.
* Support for looking at JunOS based on model, junos-mx and junos-qfx. The main support in MX is support for gathering MAC address from VPLS. 
* Mac show now handles VPLS MAC instance filtering in addition to the existing filters. 
* Support for new table, FS, for file system. Support for gathering FS data from all supported platforms now: Cisco, Cumulus, Juniper, Arista and Linux servers. You can filter FS output based on mountpoint, used percentages.
* ``table describe`` now supports displaying any description associated with each field. Only path table has descriptions added in this release. But the code is such that a user can edit the schema file, add the description and have the result show up without writing any code.
* **ALPHA FEATURE** topology table continues to see improvements and additions. We've added support for displaying ARP neighbors in the topology view.


## 0.3 (July 15, 2020)

First release with third-party contributions! Vastly improved support for realistic NXOS data center deployments. Besides 32 closed bugs, main enhancements include:

* Improved EVPN assert to handle cases of using routed multicast underlay instead of ingress replication. This includes checking for consistency of multicast groups for a VNI across nodes.
* NXOS support for EVPN requires version 9.3.3 or later as there's no command to get certain info about all VNI, only by specific VNI values.
* Improved EVPN show and summarize to include more meaningful outputs.
* Improved BGP and OSPF assert. BGP assert especially handles a few more cases such as when peering is not over directly connected interfaces. 
* **ALPHA feature** Support for topology as a first class resource. Try topology show and topology summarize. This is still work in progress.
* Support for the poller to gather data from nodes and saving them to a directory. You can then run the poller to read this directory to build the output files.

## 0.2.1 (June 18, 2020)

The main issue was fixing a bug in Junos BGP data capture that got missed in the tests. This is issue #170. We also fixed #169 listed below as part of release 0.2. However, another Junos bug is still open

* [Bug #176](https://github.com/netenglabs/suzieq/issues/176): BGP uptime is not properly captured for Junos.

## 0.2 (June 18, 2020)

The **main features** of this release are **IPv6 support** and support for **extracting data out of NXOS and Junos** devices. A few bugs have been fixed. **The parquet data format has changed and will change for one more release before we start supporting an evolving schema format.** If you have significant or important data saved with the older release, please reach out to us for specific assistance.

More specific release notes and caveats emptor are:

* Not all asserts work with Junos devices because of the LLDP information provided as a result of the default configuration. 
* No testing has been done at scale. Please reach out to us if you're running into problems with polling either a large number of devices or pulling a lot of data (routes, macs, arp/nd usually) from a single device. If you don't run into any problems doing so, please reach out to us to let us know about your success.

* [Bug #169](https://github.com/netenglabs/suzieq/issues/169) which shows an incorrect error message in the poller log file




