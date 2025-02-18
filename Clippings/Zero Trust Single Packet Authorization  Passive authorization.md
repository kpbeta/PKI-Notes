---
title: "Zero Trust: Single Packet Authorization | Passive authorization"
source: https://network-insight.net/2019/06/18/zero-trust-single-packet-authorization-passive-authorization/
author:
  - "[[Matt Conran]]"
published: 2019-06-18
created: 2025-02-17
description: SPA Single Packet Authorization is a zero trust-technology. Single Packet Authentication ensures authorization before responding to a packet.
tags:
  - clippings
  - "#single_packet_inspection"
  - fwknop
  - port_knocking
  - "#zerotrust"
---
**1)** SPA is a security technique that allows secure access to a protected resource by requiring the sender to authenticate themselves through a single packet. Unlike traditional methods that rely on complex authentication processes, SPA simplifies the process by utilizing cryptographic techniques and firewall rules.

**2)** Let’s explore SPA’s inner workings to better comprehend it. When an external party attempts to gain access to a protected resource, SPA requires them to send a specially crafted packet containing authentication credentials.

**3)** This packet is unique and can only be understood by the intended recipient. The recipient’s firewall analyzes this packet and grants access if the credentials are valid. This streamlined approach enhances security and reduces the risk of brute-force attacks and unauthorized access attempts.

### **Zero Trust Security** 

Strong authentication and encryption suites are essential components of zero-trust network security. A zero-trust network assumes a hostile network environment. This book does not make specific recommendations about which suites provide strong security that will stand the test of time. However, you can use the NIST encryption guidelines to choose strong cipher suites based on security standards.

The types of suites system administrators can choose from may be limited by device and application capabilities. The security of these networks is compromised when administrators weaken these suites.

> **Authentication MUST be performed on all network flows.**

Zero-trust networks immediately suspect all packets. Before their data can be processed, they must be rigorously examined. As a primary means of accomplishing this, we rely on authentication. For network data to be trusted, its provenance must be authenticated. Without it, zero-trust networks are impossible. We would need to trust it if the network weren’t possible.

#### **Example: Port Knocking**

**\*\*Understanding Zero Trust Port Knocking\*\***

Zero trust port knocking is an advanced security mechanism that combines the principles of zero trust architecture with the traditional port knocking technique. Unlike conventional port knocking, which relies on a sequence of network connection attempts to open a port, zero trust port knocking requires authentication and verification at every step. This method ensures that only authorized users can access specific network resources, reducing the attack surface and enhancing overall security.

**\*\*How Zero Trust Port Knocking Works\*\***

At its core, zero trust port knocking operates on the principle of “never trust, always verify.” Here’s a simplified breakdown of how it works:

1\. \*\*Pre-Knock Authentication\*\*: Before any port knocking sequence begins, users must authenticate their identity using multi-factor authentication (MFA) or other verification methods.

2\. \*\*Dynamic Port Sequences\*\*: Once authenticated, users initiate a sequence of port knocks. These sequences are dynamic and change periodically to prevent unauthorized access.

3\. \*\*Access Control Policies\*\*: The system checks the user’s credentials and the knock sequence against predefined access control policies. Only valid combinations grant access to the requested resources.

4\. \*\*Logging and Monitoring\*\*: All activities are logged and monitored in real-time, providing insights and alerts for suspicious behavior.

[![](https://network-insight.net/wp-content/uploads/2024/05/rsz_1portknocking.png)](https://network-insight.net/wp-content/uploads/2024/05/rsz_1portknocking.png) [![](https://network-insight.net/wp-content/uploads/2024/05/rsz_1portknocking2.png)](https://network-insight.net/wp-content/uploads/2024/05/rsz_1portknocking2.png)

#### **The Role of Authorization:**

Authorization is arguably the most critical process in a zero-trust network, so an authorization decision should not be taken lightly. Ultimately, every flow and request will require a decision. For the authorization decision to be effective, enforcement must be in place. In most cases, it takes the form of a load balancer, a proxy, or a firewall. We use the policy engine to decide which interacts with this component.

The enforcement component ensures that clients are authenticated and passes context for each flow/request to the policy engine. By comparing the request and its context with policy, the policy engine informs the enforcer whether the request is permitted. As many enforcement components as possible should exist throughout the system and should be close to the workload.

#### **Advantages of SPA in Achieving Zero Trust Security**

To implement SPA effectively, several key components come into play. These include a client-side tool, a server-side daemon, and a firewall. Each element plays a crucial role in the authentication process, ensuring that only legitimate users gain access to the network resources.

**– Enhanced Security:** SPA acts as an additional layer of defense, significantly reducing the attack surface by keeping ports closed until authorized access is requested. This approach dramatically mitigates the risk of unauthorized access and potential security breaches.

**– Stealthiness:** SPA operates discreetly, making it challenging for potential attackers to detect the service’s existence. The closed ports appear as if they don’t exist, rendering them invisible to unauthorized entities.

**– Scalability:** SPA can be easily implemented across various services and devices, making it a versatile solution for achieving zero-trust security in various environments. Its flexibility allows organizations to adopt SPA within their infrastructure without significant disruptions.

**– Protection against Network Scans:** Traditional authentication methods are often vulnerable to network scans that attempt to identify open ports for potential attacks. SPA mitigates this risk by rendering the network invisible to scanning tools.

**– DDoS Mitigation:** SPA can effectively mitigate Distributed Denial of Service (DDoS) attacks by rejecting packets that do not adhere to the predefined authentication criteria. This helps safeguard the availability of network services.

### **\*\*Reverse Security & Authenticity\*\*** 

Even though we are looking at disruptive technology to replace the virtual private network and offer secure segmentation, one thing to keep in mind with [zero trust network design](https://network-insight.net/2022/07/29/zero-trust-network-design/) and [software defined perimeter](https://network-insight.net/2019/06/21/software-defined-perimeter-sdp-a-disruptive-technology/) (SDP) is that it’s not based on entirely new protocols, such as the use of spa single packet authorization and single packet authentication. So we have reversed the idea of how TCP connects.

It started with authentication and then a connected approach, but traditional networking and protocols still play a large part. For example, we still use encryption to ensure only the receiver can read the data we send. We can, however, use encryption without authentication, which validates the sender.

#### **\*\*The importance of authenticity\*\***

However, the two should go together to stand any chance in today’s world. Attackers can circumvent many firewalls and secure infrastructure. As a result, message authenticity is a must for zero trust, and without an authentication process, a bad actor could change, for example, the ciphertext without the reviewer ever knowing.

#### **\*\*Encryption and authentication\*\***

Even though encryption and authenticity are often intertwined, their purposes are distinct. By encrypting your data, you **ensure confidentiality**\-the promise that only the receiver can read it. Authentication aims to **verify** that the message was sent by what it claims to be. It is also interesting to note that authentication has another property.

Message authentication requires integrity, which is essential to validate the sender and ensure the message is unaltered. Encryption is possible without authentication, though this is a poor security practice.

#### **Example Technology: Authentication with Vault**

**\### Why Authentication Matters**

Authentication is the gateway to security. Without a reliable authentication method, your secrets are as vulnerable as an unlocked door. Vault’s authentication ensures that clients prove their identity before accessing any secrets. This process minimizes the risk of unauthorized access and potential data breaches. Vault supports a myriad of authentication methods, from token-based to more complex identity-based systems, ensuring flexibility and security tailored to your needs.

**\### Exploring Vault’s Authentication Methods**

Vault offers several authentication approaches to suit varied requirements:

1\. \*\*Token Authentication\*\*: A simple method where clients are granted a token, acting as a key to access secrets. Tokens can be easily revoked, making them an ideal choice for temporary access needs.

2\. \*\*AppRole Authentication\*\*: Designed for applications or machines, AppRole provides a role-based method where client applications authenticate using a combination of role ID and secret ID.

3\. \*\*Userpass Authentication\*\*: A straightforward username and password method, suitable for human users needing access to Vault.

4\. \*\*LDAP, GitHub, and Cloud Auth\*\*: Vault integrates seamlessly with existing enterprise systems like LDAP, GitHub, and various cloud providers, allowing users to authenticate using familiar credentials.

Each method comes with its own set of configuration options and use cases, allowing organizations to choose what best suits their security posture.

[![Vault](https://network-insight.net/wp-content/uploads/2024/10/rsz_vault1.png)](https://network-insight.net/wp-content/uploads/2024/10/rsz_vault1.png) [![](https://network-insight.net/wp-content/uploads/2024/10/rsz_vault2.png)](https://network-insight.net/wp-content/uploads/2024/10/rsz_vault2.png) [![](https://network-insight.net/wp-content/uploads/2024/10/rsz_vault3.png)](https://network-insight.net/wp-content/uploads/2024/10/rsz_vault3.png)

**Related:** Before you proceed, you may find the following post helpful:

1. [Identity Security](https://network-insight.net/2023/03/09/identity-security/)
2. [Zero Trust Access](https://network-insight.net/2019/07/22/safe-t-a-progressive-approach-to-zero-trust-access/)

### **SPA: A Security Protocol**

Single Packet Authorization (SPA) is a security protocol allowing users to access a secure network without entering a password or other credentials. Instead, it is an authentication protocol that uses a single packet—an encrypted packet of data—to convey a user’s identity and request access. This packet can be sent over any network protocol, such as TCP, UDP, or SCTP, and is typically sent as an **additional layer of authentication** beyond the network and application layers.

SPA works by having the user’s system send a single packet of encrypted data to the authentication server. The **authentication server** then uses a unique algorithm to decode the packet containing the user’s identity and request for access. If the authentication is successful, the server will send a response packet that grants access to the user.

SPA is a secure and efficient way to authenticate and authorize users. It eliminates the need for multiple authentication methods and sensitive data storage. SPA is also more secure than traditional authentication methods, as the encryption used in **SPA is often more secure than passwords or other credentials.**

Additionally, since the packet sent is encrypted, it cannot be intercepted and decoded, making it an even more secure form of authentication.

[![single packet authorization](https://network-insight.net/wp-content/uploads/2024/03/rsz_1single_packet_authorization.png)](https://network-insight.net/wp-content/uploads/2024/03/rsz_1single_packet_authorization.png)

#### \*\*The Mechanics of SPA\*\*

SPA operates by employing a shared secret between the client and server. When a client wishes to access a service, it generates a packet containing a specific data sequence, including a timestamp, payload, and cryptographic hash. The server, equipped with the shared secret, checks the received packet against its calculations. The server grants access to the requested service if the packet is authentic.

**Implementing SPA:**

Implementing SPA requires deploying specialized software or hardware components that support the single packet authorization protocol. Several open-source and commercial solutions are available, making it feasible for organizations of all sizes to adopt this innovative security technique.

#### **Back to Basics: Zero Trust**

Five fundamental assertions make up a zero-trust network:

- Networks are always assumed to be hostile.
- The network is always at risk from external and internal threats.
- To determine trust in a network, locality alone is not sufficient.
- A network flow, device, or user must be authenticated and authorized.
- Policies must be dynamic and derived from as many data sources as possible to be effective.

Different networks are divided into firewall-protected zones in a traditional network security architecture. Each zone is permitted to access network resources based on its level of trust. This model provides a solid defense in depth. In DMZs, traffic can be tightly monitored and controlled over riskier resources, like those facing the public internet.

### **Perimeter Defense**

**a)** Perimeter defenses protecting your network are less secure than you might think. Hosts behind the firewall have no protection, so when a host in the “trusted” zone is breached, which is just a matter of time, access to your data center can be breached. The zero-trust movement strives to solve the inherent problems of placing our faith in the network.

**b)** Instead, it is possible to secure network communication and access so effectively that the physical security of the transport layer can be reasonably disregarded.

**c)** Typically, we examine the remote system’s IP address and ask for a password. Unfortunately, these strategies alone are insufficient for a zero-trust network, where attackers can communicate from any IP and insert themselves between themselves and a trusted remote host.

**d)** Therefore, utilizing strong authentication on every flow in a zero-trust network is vital. The most widely accepted method is a standard named X.509.

![zero trust security](https://network-insight.net/wp-content/uploads/2022/07/rsz_2zt.png)

Diagram: Zero trust security. Authenticate first and then connect.

A key aspect of [zero-trust network ZTN](https://network-insight.net/2018/09/18/zero-trust-network-ztn/) and zero-trust principles is authenticating and authorizing network traffic, i.e., the flows between the requesting resource and the intended service. Simply securing communications between two endpoints is not enough. Security pros must ensure that each flow is authorized.

This can be done by implementing a combination of security technologies such as **Single Packet Authorization (SPA), Mutual Transport Layer Security (MTLS), Internet Key Exchange (IKE), and IP security (IPsec).**

IPsec can use a unique security association (SA) per application; only authorized flows can construct security policies. While IPsec is considered to operate at Layer 3 or 4 in the open systems interconnection (OSI) model, application-level authorization can be carried out with X.509 or an access token.

### **Mutually authenticated TLS (MTLS)**

Mutually authenticated TLS (Transport Layer Security) is a system of cryptographic protocols used to establish secure communications over the Internet. It guarantees that the client and the server are who they claim to be, ensuring secure communications between them. This authentication is accomplished through digital certificates and public-private key pairs.

Mutually authenticated TLS is also essential for preventing man-in-the-middle attacks, where a malicious actor can intercept and modify traffic between the client and server. Without mutually authenticated TLS, an attacker could masquerade as the server and thus gain access to sensitive data.

#### Setting Up MTLS

To set up mutually authenticated TLS, t**he client and server must have digital certificates**. The server certificate is used to authenticate the server to the client, while the client certificate is used to authenticate the client to the server. Both certificates are signed by the Certificate Authority (CA) and can be stored in the server and client’s browsers. The client and server then exchange the certificates to authenticate each other.

The client and server can securely communicate once the certificates have been exchanged and verified. Mutually authenticated TLS also provides encryption and integrity checks, ensuring the data is not tampered with in transit.

#### Enhanced Version of TLS

This enhanced version of TLS, known as mutually authenticated TLS (MTLS), is used to validate both ends of the connection. The most common TLS configuration is the validation, which ensures the client is connected to a trusted entity. However, the authentication doesn’t happen the other way around, so the remote entity communicates with a trusted client. This is the job of mutual TLS. As I said, mutual TLS goes one step further and authenticates the client.

### **The pre-authentication stage**

You can’t attack what you cannot see. The mode that allows pre-authentication is Single Packet Authorization. UDP is the preferred base for pre-authentication because UDP packets, by default, do not receive a response. However, TCP and even ICMP can be used with the SPA. Single Packet Authorization is a next-generation passive authentication technology beyond what we previously had with port knocking, which uses closed ports to identify trusted users. SPA is a step up from port knocking.

#### Port-Knocking Scenario

The typical port-knocking scenario involves a port-knocking server configuring a packet filter to block all access to a service, such as the SSH service until a port-knocking client sends a **specific port-knocking sequence.** For instance, the server could require the client to send TCP SYN packets to the following ports in order: 23400 1001 2003 65501.

If the server monitors this knock sequence, the packet filter reconfigures to allow a connection from the originating IP address. However, port knocking has its limitations, which SPA addresses; **SPA retains all of the benefits of port knocking but fixes the rules**.

#### SPA & Port Knocking

As a next-generation **Port Knocking (PK),** SPA overcomes many limitations PK exhibits while retaining its core benefits. However, PK has several limitations, including difficulty protecting against replay attacks, the inability to reliably support asymmetric ciphers and HMAC schemes, and the fact that it is trivially easy to mount a DoS attack by spoofing an additional packet into a PK sequence while it is traversing the network (thereby convincing the PK server that the client does not know the proper sequence).

SPA solves all of these shortcomings. As part of SPA, services are hidden behind a default-drop firewall policy, SPA data is passively acquired (usually via libpcap), and standard cryptographic operations are implemented for SPA packet authentication and encryption/decryption.

### **Firewall Knock Operator**

Fwknop (short for the “Firewall Knock Operator”) is a single-packet authorization system designed to be a secure and straightforward way to open up services on a host running an iptables- or ipfw-based firewall. It is a free, open-source application that uses the Single Packet Authorization (SPA) protocol to provide secure access to a network.

Fwknop sends a single SPA packet to the firewall containing an encrypted message with authorization information. The message is then decrypted and compared against a set of rules on the firewall. If the message matches the rules, the firewall will open access to the service specified in the packet.

> **No need to manually configure the firewall each time**

Fwknop is an ideal solution for users who need to access services on a remote host w**ithout manually configuring the firewall each time.** It is also a great way to add an extra layer of security to already open services.

To achieve strong concealment, **fwknop implements the SPA authorization scheme**. SPA requires only a single packet encrypted, non-replayable, and authenticated via an HMAC to communicate desired access to a service hidden behind a firewall in a default-drop filtering stance. The main application of SPA is to use a firewall to drop all attempts to **connect to services such as SSH** to make exploiting vulnerabilities (both 0-day and unpatched code) more difficult. Because there are no open ports, any service SPA hides cannot be scanned with, for example, NMAP.

**Supported Firewalls:**

The fwknop project supports four firewalls: Support iptables, firewall, PF, and ipfw across Linux, OpenBSD, FreeBSD, and Mac OS X. We also support custom scripts so that fwknop can be made to help other infrastructure, such as upset or nftables.

![fwknop client user interface ](https://network-insight.net/wp-content/uploads/2019/06/rsz_spa_interface.png)

Diagram: fwknop client user interface. [Source mrash GitHub.](https://github.com/mrash/fwknop)

### **Example use case: SSHD protection**

Users of Single Packet Authorization (SPA) or its less secure cousin, Port Knocking (PK), usually access SSHD running on the same system as the SPA/PK software. A SPA daemon temporarily permits access to a passively authenticated SPA client through a firewall configured to drop all incoming SSH connections. This is considered the primary SPA usage.

In addition to this primary use, fwknop also makes robust use of NAT (for iptables/firewalld firewalls). A firewall is usually deployed on a single host and acts as a gateway between networks. Firewalls that use NAT (at least for IPv4 communications) commonly provide Internet access to internal networks on RFC 1918 address space and access to internal services by external hosts.

Since fwknop integrates with NAT, users on the external Internet can access internal services through the firewall using SPA. Additionally, it allows fwknop to support cloud computing environments such as Amazon’s AWS, although it has many applications on traditional networks.

![SPA Use Case](https://network-insight.net/wp-content/uploads/2019/06/rsz_use_case_for_spa.png)

Diagram: SPA Use Case. [Source mrash Github.](https://github.com/mrash/fwknop)

#### **Single Packet Authorization and Single Packet Authentication**

Single Packet Authorization (SPA) uses proven cryptographic techniques to make internet-facing servers invisible to unauthorized users. Only devices seeded with the cryptographic secret can generate a valid SPA packet and establish a network connection. This is how it reduces the attack surface and becomes invisible to hostile reconnaissance.

SPA Single Packet Authorization was invented over ten years ago and was commonly used for superuser SSH access to servers where it mitigates attacks by unauthorized users. The SPA process happens before the TLS connection, mitigating attacks targeted at the TLS ports.

As mentioned, SDP didn’t invent new protocols; it was more binding existing protocols. SPA used in SDP was based on RFC 4226 HMAC-based One-Time Password “HOTP.” It is another layer of security and is not a replacement for the security technologies mentioned at the start of the post.

#### **Surveillance: The first step**

The first step in an attack is reconnaissance, whereby an attacker is on the prowl to locate a target. This stage is easy and can be automated with tools such as NMAP. However, SPA ( and port knocking ) employs a default-drop stance that provides service only to those IP addresses that can prove their identity via a passive mechanism.

No TCP/IP stack access is required to authenticate remote IP addresses. Therefore, NMAP cannot tell that a server is running when protected with SPA, and whether the attacker has a zero-day exploit is irrelevant.

#### Process: Single Packet Authentication

The idea around SPA and **Single Packet Authentication** is that a single packet is sent, and based on that packet, an authentication process is carried out. The critical point is that nothing is listening on the service, so you have no open ports. For the SPA service to operate, there is nothing explicitly listening.

When the client sends an SPA packet, it will be rejected, but a second service identifies it in the IP stack and authenticates it. If the SPA packet is successfully authenticated, the server will open a port in the firewall, which could be based on Linux iptables, so that the client can establish a secure and encrypted connection with the intended service.

> **A simple Single Packet Authentication process flow**

The [SDP network](https://network-insight.net/2019/06/17/sdp-network/) gateway protects assets, and this component could be containerized and listened to for SPA packets. In the case of an open-source version of SDP, this could be fwknop, which is a widespread open-source SPA implementation. When a client wants to connect to a web server, it sends a SPA packet. When the requested service receives the SPA packet, it will open the door once the credentials are verified. However, the service still has not responded to the request.

When the **fwknop services receive a valid SPA packet**, the contents are decrypted for further inspection. The inspection reveals the protocol and port numbers to which the sender requests access. Next, the SDP gateway adds a rule to the firewall to establish a mutual TLS connection to the intended service. Once this mutual TLS connection is established, the SDP gateway removes the firewall rules, making the service invisible to the outside world.

![single packet authorization](https://network-insight.net/wp-content/uploads/2019/06/rsz_spa1.png)

Diagram: Single Packet Authorization: The process flow.

Fwknop uses this information to open firewall rules, allowing the sender to communicate with that service on those ports. The firewall will only be opened for some time and can be configured by the administrator. Any attempts to connect to the service must know the SPA packet, and even if the packet can be recreated, the packet’s sequence number needs to be established before the connection. This is next to impossible, considering the sequence numbers are randomly generated.

Once the firewall rules are removed, let’s say after 1 minute, the initial MTLS session will not be affected as it is already established. However, other sessions requesting access to the service on those ports will be blocked. This permits only the sender of the IP address to be tightly coupled with the requested destination ports. It’s also possible for the sender to include a source port, enhancing security even further.

#### **What can Single Packet Authorization offer**

Let’s face it: robust security is hard to achieve. We all know that you can never be 100% secure. Just have a look at OpenSSH. Some of the most security-conscious developers developed OpenSSH, yet it occasionally contains exploitable vulnerabilities.

Even when you look at some attacks on TLS, we have already discussed the DigiNotar forgery in a previous post on zero-trust networking. Still, one that caused a significant issue was the THC-SSL-DOS attack, where a single host could take down a server by taking advantage of the asymmetry performance required by the TLS protocol.

Single Packet Authorization (SPA) overcomes many existing attacks and, combined with the enhancements of MTLS with pinned certificates, creates a robust security model addition; SPA defeats many a DDoS attack as only a limited amount of server performance is required to operate.

**SPA provides the following security benefits to the SPA-protected asset:**

- - **SPA blackens the gateway and protects assets** that sit behind the gateway. The gateway does not respond to connection attempts until it provides an authentic SPA. Essentially, all network resources are dark until security controls are passed.
- **SPA also mitigates DDoS attacks on TLS**. TLS is likely publicly reachable online, and running the HTTPS protocol is highly susceptible to DDoS. SPA mitigates these attacks by allowing the SDP gateway to discard the TLS DoS attempt before entering the TLS handshake. As a result, there will be no exhaustion from targeting the TLS port.
- **SPA assists with attack detection**. The first packet to an SDP gateway must be a SPA packet. If a gateway receives any other type of packet, it should be viewed and treated as an attack. Therefore, the SPA enables the SDP to identify an attack based on a malicious packet.