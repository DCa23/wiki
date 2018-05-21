
Research projects interesting for eXO and/or guifi and/or international community networks. This projects could be done by its community, external/conctracted professionals or academia deliverables

important notes: 

- this document is a draft
- information here could be incorrect and/or outdated

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**

- [Decentralized DNS](#decentralized-dns)
- [Solar node](#solar-node)
- [Secure mesh](#secure-mesh)
- [Peerstreamer](#peerstreamer)
- [Retroshare](#retroshare)
- [Improve wireguard](#improve-wireguard)
- [Application to promote socialization between community network members](#application-to-promote-socialization-between-community-network-members)
- [raspberry pi + spi interface](#raspberry-pi--spi-interface)
- [Community network](#community-network)
  - [(1) Network descriptor (proof of concept)](#1-network-descriptor-proof-of-concept)
  - [(2 and 3) Template-based firmware](#2-and-3-template-based-firmware)
- [Online judge programming challenge resource](#online-judge-programming-challenge-resource)
- [Improve identity management in community networks](#improve-identity-management-in-community-networks)
- [Open source implementation of TDMA for Linux kernel](#open-source-implementation-of-tdma-for-linux-kernel)
- [Open source implementation of 802.1aq for Linux kernel](#open-source-implementation-of-8021aq-for-linux-kernel)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Decentralized DNS

Complexity: feasible for a developer

A (simple?) decentralized DNS solutions that fits in embedded devices (lightweight) to have FQDN services with letsencrypt certificates inside the community network. Interesting additional features: revision control/snapshots and trusting framework to improve control of the registers and avoid attacks.

Candidates:

- https://github.com/mwarning/KadNode
- https://github.com/torrentkino/torrentkino
- https://github.com/HarryR/ffff-dnsp2p

# Solar node

Complexity: feasible for an integrator

OpenMPPT tracker, [presentation](https://github.com/elektra42/freifunk-open-mppt/blob/master/Freifunk-Open-MPPT-English.pdf)

18 V SOLAR PANEL 50 WATT [1](https://www.amazon.de/enjoysolar%C2%AE-Polykristallin-Solarmodul-Solarpanel-Wohnmobil/dp/B076B3Z48G?SubscriptionId=AKIAILSHYYTFIVPWUY6Q&tag=duckduckgo-ffsb-de-21&linkCode=xm2&camp=2025&creative=165953&creativeASIN=B076B3Z48G)

battery example [1](https://www.reichelt.com/de/en/Lead-Acid-Batteries-Kung-Long/WP-18-12/3/index.html?ACTION=3&LA=2&ARTICLE=44425&GROUPID=4232&artnr=WP+18-12&trstct=pol_14&SID=94WvXc46wQATMAAHIShQMfc78bb6a5f6916d42ae1066844b9e377) [2](https://www.amazon.de/LONG-Bleiakku-WP18-12I-12V-18Ah/dp/B0746J9F6Z?SubscriptionId=AKIAILSHYYTFIVPWUY6Q&tag=duckduckgo-ffhp-de-21&linkCode=xm2&camp=2025&creative=165953&creativeASIN=B0746J9F6Z)

Antena of 3W: tplink 841, tplink 510 (TODO: requires compatibility with latest version)

Translation needed from German to English -> https://elektrad.info/download/Freifunk-OpenMPPT-Handbuch-26-August-2017.pdf

Origin: Elektra

# Secure mesh

note: I don't know if DFS stuff from secure mesh should be separated, probably later when we have more details. The thing is that they go together because they need wpasupplicant

Complexity: feasible for an integrator, good developer skills are very welcome (probably requires to read source code during the documentation phase)

Configuration and documentation about a network that is very secure and DFS ready

- Encrypted links
- bmx7 with its "trusted routing"

Origin: Daniel Gölle

extra: [DFS](../howto/dfs.md)

# Peerstreamer

Complexity: feasible for a developer and/or and integrator

- Clustering in the serverside
- How to put the streaming in RTP, how to play it?
- Get capture screen working, use cases:
    - get scream of presenter in a conference through web application (no specific cable: VGA, HDMI, etc.)
    - twitch.tv style service in a decentralized manner

Origin: netcommons.eu / http://peerstreamer.org/

# Retroshare

Complexity: feasible for an integrator, probably requires documentation and workshops 

Discard nextcloud to upload massive content and other similar services. This puts risks on system administrators: it is better an encrypted P2P file sharing system

Origin: Gioacchino Mazzurco / http://retroshare.net/

# Improve wireguard

Complexity: feasible for a developer but requires creative alternative way to solve the problem

Wireguard is not prepared yet for community networks. It uses a global clock and requires routers to synchronize NTP before they connect (chicken-egg problem)

Meanwhile, to mitigate the issue somehow, you can have an internal ntp server

# Application to promote socialization between community network members

Complexity: feasible for a developer

Inspired by [tubechat](http://tube.chat/) presentation in battlemesh, an application that improves the relation and interaction between the people involved in the community network would be an interesting application. Creative ways so they discover them on the same network they are; and ways to keep the contact.

# raspberry pi + spi interface

Complexity: the skills needed are just find howtos and test them

Document the way to flash rom of bricked routers using SPI interface connector through the GPIO interface of raspberry pi and `flashrom` program

my notes:

```
reflashing eeprom of a device that is not booting

https://raspberrypi-aa.github.io/session3/spi.html
https://pinout.xyz/pinout/spi#

the command looks like this but can contain errors

  flashrom -p linux_spi:/dev/spidev0.0 spispeed=1000 -w /path/to/the/thing -V
```

[extra notes](../howto/spi.md)

# Community network

Research on the essential tools very important for a community network

## (1) Network descriptor (proof of concept)

Complexity: feasible for a developer

This section assumes that autoconfiguration methodologies (libremesh, gluon) or a specific centralized technology (guifi.net's drupal) does not help to:

- get more people involved
- complexity equal to simple
- maintenance

The proposed approach is to get consensus on the full specification / description of a network. [Netjson](https://netjson.org) is a good try, but only describes specific nodes and its links. It is required the data of a zone as seen in guifi webpage

The proposition is to write a proof of concept tool in python where in a git repository (another research could include using other P2P/decentralized solutions like IPFS/IPLD, etc.):

- a zone is a directory
- `.zone` is a file that describes the zone: IP delegation to this node, who is admin or responsible, etc.
- a node is a file described in netjson format

note: when this description grows will go to a git repo itself

Inspiration:

- password-store.org
- netjson.org
- guifi webpage & cnml
- yanosz pointed me to:
    - python script to run VPNs https://github.com/freifunk/icvpn
    - data: https://github.com/freifunk/icvpn-meta


Origin: guifipedro

## (2 and 3) Template-based firmware

Complexity: feasible for a developer or integrator

Get one firmware for a specific device in a static oriented way (no autoconfiguration or the need of firmwares like libremesh or gluon)

- (2) Compilation through specific profile (device) and the packages required by its role => https://git.kbu.freifunk.net/yanosz/node-config-feed/
- (3) Template configuration: /etc/config => https://github.com/yanosz/mesh_testbed_generator/

quality measure: mark specific commit. fork relevant git repositories? routing?

Inspiration: Yanosz

Origin: guifilab / barcelona community

We started this => https://github.com/guifi-exo/temba

# Online judge programming challenge resource

Educational and brain-fitness project to improve programming and developer skills for community network folks

# Improve identity management in community networks

The more services you have in the network, the more registers and logins users have to do. But well, this is a general problem that affects all organizations when use open source products and cannot spend time integrating the different services.

LDAP allows you to use always the same user and password. One user that have to login multiple times

SAML theoretically solves two problems better than LDAP:

- (optional) Single Sign On (SSO)
- Identity federation: "I don't know who are you, but let me point to the correct authority to validate you"

The project can revisit LDAP technology (a good WebGUI that is secure) or introduce SAML usage

Common LDAP service is `slapd` or openldap

I have never tried SAML software, but I found 4 open source implementation (all of them in java, brrr):

- https://www.shibboleth.net/
- this will be used by spanish universities http://adas-sso.com/en/
- https://www.keycloak.org/
- https://www.gluu.org/

# Open source implementation of TDMA for Linux kernel

Complexity: unknown

http://www.netshe.ru/tdma

Test [netshe products](http://www.netshe.ru/products) (accessed 2018-05-18)

```
NETSHe

    The OpenWRT derivative with compatible SDK and a set of unique software packages (like WebUI, initialization subsystem, wireless extensions) to create firmwares for wireless, networks and embedded devices. 

Modified wireless stack

    Modified wireless stack for Linux. Includes TDMA, Frequency, shifting and compression extensions. 

TDMA

    Implementation of time division multiply access for ordinary 802.11a/b/g/n hardware and Linux mac80211 wireless stack 
```

more info about [channel access](channel-access.md)

# Open source implementation of 802.1aq for Linux kernel

Complexity: unknown

https://en.wikipedia.org/wiki/IEEE_802.1aq

The "new" ethernet standard works mostly as MPLS (useful for multitenant network) but it is backward compatible with the old ethernet protocols which reduce its overall complexity. At the moment is not available in Linux platforms

