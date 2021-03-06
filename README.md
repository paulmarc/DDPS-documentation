
# DeiC Distributed Denial of Service Attack Prevention System, DDPS

DDPS stands for _DeiC DDoS Protection Service_ and it has been built for the Danish
research network, DeIC. The full vision statement is available as part of
[the original project proposal (in danish)](docs/DDoS_Detection_and_Mitigation_Service_MB.md)

DDPS is conceived as an automated system for DDoS mitigation: it detects attacks
and automatically triggers mitigation.        
The mitigation is based on the creation of rules by a detection engine which
can filter out unwanted traffic (see below).         
Additionally, end-users may _add_, _edit_, or _cancel_ mitigation rules as well as
view _archived rules_ and _statistical information_.        
Based on BGP Flowspec, it is intended to be used in a system where _detection_ is
placed as _close as possible to the target_ and _mitigation_ is
placed as _close as possible to the source(s) of the attack_ in order to limit
in particular the impact of volumetric attacks.

The project is hosted on github.com and split in a number of sub-projects. This is
the documentation hub. Links to the sub-projects are:

  - [DDPS fastnetmon installation and configuration](https://github.com/deic-dk/DDPS-fastnetmon)
  - [DDPS database daemon](https://github.com/deic-dk/DDPS-db2dps)
  - [DDPS NODE](https://github.com/deic-dk/ddps-node)
  - [DDPS web-user interface](https://github.com/deic-dk/gossamer)
  - [DDPS Customer Site Simulation](https://github.com/deic-dk/DDPS-customer)
  - [DDPS DDoS simulator](https://github.com/deic-dk/DDPS-attacker)

## Short technology overview

A DDoS mitigation system based on BGP Flowspec requires that some rules 
_enter_ the system, and that other rules are _sent to and enforced by peering
partners and upstream providers_. The drawing illustrates the different
components: to the left is a customer site where the detection engine (using
fastnetmon) monitors traffic to a group of networks and logs traffic statistics
to a local influx database.       
When an attack is detected, a set of mitigation rules are generated and sent to
DDPS via an encrypted channel. The rules are added to an database and announced
as flowspec rules in the BGP session. This means that they are both sent to
upstream networks and disseminated across the network to edge routers. The
rules are enforced on edge on the routers thereby mitigating the attack. Rules
are later withdrawn.

![](docs/assets/img/workflow.png)

<!--
	the image is made in Draw.io (chrome app), the src is in media/docs/workflow.xml
-->

The rules have to match the BGP community we are responsible for: they have to
match our network only. The system has been designed to do that from the
entrance point to the exit point: customers can only make rules for their own
network which is a subset of ours, and the system can only send rules upstream
which matches our network as such.

Rules are made up of the 12 fields defined in
[RFC5575](https://tools.ietf.org/html/rfc5575) and
[RFC7674](https://tools.ietf.org/html/rfc7674) with a start and expire time
together with some customer information.

Rules are added to the system from two sources:

  - a *policy editor* operated by the customer
  - an *automatic detection engine* based on
    [fastnetmon](https://github.com/pavel-odintsov/fastnetmon)

Rules are uploaded to the database server and added to a
[postgres](https://www.postgresql.org) database. A daemon queries the database
for new and expired rules. The daemon converts the rules to BGP flowspec
announce and withdraw rules and inserts them in two
[exabgp](https://github.com/Exa-Networks/exabgp) instances from where they are
sent to our edge routers, peers and upstream provider.

## Contact and Contributing

If you would like help implementing DDPS then please have a look at the
[contributing](docs/contributing.md) page. If you would like to contact
the team behind DDPS then send an email to [ddps-info@deic.dk](mailto:ddps-info@deic.dk)

## Find out more

  - [Project Proposal for version 1.0 / what does DDPS solve and with what limitations](docs/DDoS_Detection_and_Mitigation_Service_MB.md) (in Danish only)
  - [Technical Design for version 1.0](docs/DDPS-technical-documentation.md)
  - TODO: [System Documentation](docs/stubfile.md)
  - TODO: [Todo / Road map](docs/stubfile.md)
  - [Status](docs/status-notes.md) for version 1.0
  - TODO: [FAQ](docs/stubfile.md)
  - TODO: [Known issues](docs/stubfile.md)
  - How to get support / Contact information: please write to [ddps-info@deic.dk](mailto:ddps-info@deic.dk)

## License

DDPS is copyright 2015-2017 DeiC, Denmark

Licensed under the [Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0)
(the "License"); you may not use this software except in compliance with the
License.

Unless required by applicable law or agreed to in writing, software distributed
under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
CONDITIONS OF ANY KIND, either express or implied. See the License for the
specific language governing permissions and limitations under the License.


