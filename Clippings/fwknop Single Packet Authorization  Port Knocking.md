---
title: "fwknop: Single Packet Authorization > Port Knocking"
source: "https://www.cipherdyne.org/fwknop/"
author:
published:
created: 2025-02-17
description: "Cipherdyne System and Network Security"
tags:
  - "clippings"
---
#port_knocking #fwknop

**fwknop** stands for the "FireWall KNock OPerator", and implements an authorization scheme called [**Single Packet Authorization (SPA)**](https://www.cipherdyne.org/fwknop/docs/SPA.html). This method of authorization is based around a default-drop packet filter (fwknop supports [iptables](http://www.netfilter.org/) and firewalld on Linux, ipfw on FreeBSD and Mac OS X, and PF on OpenBSD) and [libpcap](http://www.tcpdump.org/). SPA is essentially next generation port knocking (more on this below). The design decisions that guide the development of fwknop can be found in the blog post ["Single Packet Authorization: The fwknop Approach"](https://www.cipherdyne.org/blog/2012/09/single-packet-authorization-the-fwknop-approach.html).

- [Download](https://www.cipherdyne.org/fwknop/download/ "Download fwknop")    |    latest release: **2.6.11**
- [Tutorial](https://www.cipherdyne.org/fwknop/docs/fwknop-tutorial.html "fwknop Tutorial")
- [Documentation](https://www.cipherdyne.org/fwknop/docs/ "fwknop Documentation")
- [Features](https://www.cipherdyne.org/fwknop/docs/features.html "fwknop Feature List")
- [Source Code](https://github.com/mrash/fwknop "fwknop Source Code") ([github](https://github.com/mrash/fwknop "fwknop Source Code - github"))
- [Code Coverage](https://www.cipherdyne.org/fwknop/lcov-results "fwknop code coverage") (for the 2.6.10 release)
- [Mailing List](http://lists.sourceforge.net/lists/listinfo/fwknop-discuss "fwknop Mailing List")

You can clone the fwknop git repository as follows from github:

```
$ git clone https://www.github.com/mrash/fwknop fwknop.git
Cloning into 'fwknop.git'...
remote: Counting objects: 5275, done.
remote: Compressing objects: 100% (1603/1603), done.
remote: Total 5275 (delta 3672), reused 5155 (delta 3552)
Receiving objects: 100% (5275/5275), 2.07 MiB | 3.96 MiB/s, done.
Resolving deltas: 100% (3672/3672), done.
```

SPA requires only a single encrypted packet in order to communicate various pieces of information including desired access through a firewall policy and/or complete commands to execute on the target system. By using a firewall to maintain a "default drop" stance, the main application of fwknop is to protect services such as [OpenSSH](http://www.openssh.org/) with an additional layer of security in order to make the exploitation of vulnerabilities (both 0-day and unpatched code) much more difficult. **With fwknop deployed, anyone using nmap to look for SSHD can't even tell that it is listening - it makes no difference if they want to run a password cracker against SSHD or even if they have a 0-day exploit**. The authorization server passively sniffs SPA packets via libcap and hence there is no "server" to which to connect in the traditional sense. Access to a protected service is only granted after an authenticated, properly decrypted, and non-replayed packet is monitored from an fwknop client (see the following network diagram; the SSH session can only take place after the SPA packet is sniffed):

![Network diagram to illustrate the deployment of fwknop within an iptables firewall](https://www.cipherdyne.org/images/fwknop_tutorial_network_diagram.png "Network diagram to illustrate fwknop deployment")

**Single Packet Authorization** retains the benefits of [Port Knocking](http://www.portknocking.org/) (i.e. service protection behind a default-drop packet filter), but has the advantages listed below over over Port Knocking. For a complete treatment of all fwknop design goals, see the [fwknop tutorial](https://www.cipherdyne.org/fwknop/docs/fwknop-tutorial.html#design).

**- SPA can utilize asymmetric ciphers for encryption
- SPA is authenticated with an HMAC in the encrypt-then-authenticate model
- SPA packets are non-replayable
- SPA cannot be broken by trivial sequence busting attacks
- SPA only sends a single packet over the network
- SPA is much faster
**

  
More information Single Packet Authorization and port knocking can be found here:

- [Enhancing Firewalls: Conveying User and Application Identification to Network Firewalls](http://www.ciphertext.info/papers/thesis-degraaf.pdf) This is a Master's Thesis completed in May, 2007 by **Rennie deGraaf** at the The University of Calgary. See his [website](http://www.ciphertext.info/) for more information.
- [An Analysis of Port Knocking and Single Packet Authorization](http://www.securitygeneration.com/wp-content/uploads/2010/05/An-Analysis-of-Port-Knocking-and-Single-Packet-Authorization-Sebastien-Jeanquier.pdf). This is a Master's Thesis complete in September, 2006 by **Sebastien Jeanquier** at the [Royal Holloway College, University of London](http://www.rhul.ac.uk/) about the concepts of port knocking and Single Packet Authorization. See his [website](http://www.securitygeneration.com/) for more information.
- [Single Packet Authorization with fwknop](https://www.cipherdyne.org/fwknop/docs/SPA.html). This paper was published in the February, 2006 issue of [USENIX ;login: Magazine](http://www.usenix.org/publications/login/).

**fwknop** started out as a Port Knocking implementation in 2004, and at that time it was the first tool to combine traditional encrypted port knocking with passive OS fingerprinting. This made it possible to do things like only allow, say, Linux-2.4/2.6 systems to connect to your SSH daemon. However, if you are still using the port knocking mode in fwknop, I strongly recommend that you switch to the Single Packet Authorization mode.

## fwknop on Slashdot

**fwknop** has made Slashdot twice here: [Combining Port Knocking With OS Fingerprinting](http://it.slashdot.org/it/04/08/01/0436204.shtml), and here: [Going Beyond Port Knocking; Single Packet Access](http://it.slashdot.org/article.pl?sid=05/05/30/1128209&tid=172&tid=106).