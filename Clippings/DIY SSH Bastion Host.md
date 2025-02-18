---
title: DIY SSH Bastion Host
source: https://smallstep.com/blog/diy-ssh-bastion-host/
author: 
published: 
created: 2025-02-17
description: How to create and deploy a simple and minimal bastion host on Ubuntu 20.04 LTS.
tags:
  - clippings
  - "#bastion"
---
Updated on: May 20, 2024

Let's build and configure a minimal SSH bastion host (jump box) from scratch, using Ubuntu 20.04 LTS.

## What's a bastion host?

Bastion is a military term meaning "a projecting part of a fortification."

In the same way that a home WiFi router sits between the vast and perilous internet and the often insecure devices on a local network, a bastion host sits between the public internet and an internal network (a VPC, for example), acting as a gateway to reach the internal hosts while protecting them from direct exposure to the wilds of the public internet. Bastion hosts often run OpenSSH or a remote desktop server.

A bastion host serves as an important choke point in a network. Given its position, it *can* take on a lot of responsibilities: auditing and session logging, user authentication for internal hosts, and advanced threat detection. But it doesn't need to do all that. We're going to keep things simple here and build a bastion from scratch that supports the proxying of SSH connections. Then we'll talk about some fancier stuff we could do.

## Why a bastion host?

If you have an internal network and you need to reach those hosts from the public internet, a bastion host is an easy option.

Do you even need a bastion? As with nearly any decision in technology, it depends. Here are some alternatives you might consider.

## Alternatives to bastions
### Set up an IPsec VPN

If you need deeper access to your internal network than you can get with SSH or RDP, you may want a VPN. But IPsec VPNs add a lot of complexity and maintenance burden compared to the other options, including bastion hosts.

### Set up an overlay network

An overlay network is a lighter and simpler kind of VPN that supports roaming endpoints. It still takes a bit of setup. The most common open source options for this are [Wireguard](https://www.wireguard.com/) and [Nebula](https://slack.engineering/introducing-nebula-the-open-source-global-overlay-network-from-slack-884110a5579). You could run one of these at the edge of your internal network (like a VPN), or on all the hosts you want to be able to access, and tunnel SSH traffic through it. All of your clients will also need to run Wireguard or Nebula.

### Use a hosted proxy

[Google IAP](https://cloud.google.com/iap/) and [AWS Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html) are hosted solutions that tunnel SSH traffic to your internal cloud network. The benefit here is that you can use cloud IAM roles for authentication and you can implement more sophisticated security policies (security key policies, or device-level policies rather than IP-based policies) that aren't feasible if you run your own bastion host. These services are free to use, but the drawback is that IAP and AWS Session Manager are more complex than pure SSH, and they add some lock-in to GCP or AWS.

## Setting up a bastion

Let's make some assumptions:

- We only want this bastion to forward SSH connections to our internal hosts. Because we're using SSH here, users will have to authenticate both to the bastion, then to the internal host. To complete the connection, users will need valid credentials for both hosts, but *it's possible to use different credentials for the bastion and the internal host*, and we're going to take advantage of that feature.
- We'll have a single shared user for everyone, and no interactive terminal sessions allowed.
- Users will connect to internal hosts using `ssh -J [bastion] [internal host]`, or with the `ProxyJump` directive in a `Match Host` block of their `.ssh/config`.

### Launch an instance.

Stand up a Linux instance on your favorite cloud provider. We'll use Ubuntu 20.04 LTS because it is simple, it's well supported, and it includes the recently-released OpenSSH 8.2.

Set up a firewall or security group policy to restrict connections to the bastion to port 22 (SSH), and, if you can, only allow connections from IPs you trust.

### Configure the bastion

We'll need to do a few things to get our bastion ready.

##### Configure OpenSSH

We recommend enforcing [Mozilla's OpenSSH security guide](https://infosec.mozilla.org/guidelines/openssh). Unfortunately their guide only covers up to OpenSSH 6.7. Here are the guidelines that are still relevant to OpenSSH 8.2:

- **Deactivate short moduli**. Moduli are used for key exchange at the start of an SSH connection. Mozilla recommends only using 3071-bit or greater moduli for extra security. To enforce this, run:
```
awk '$5 >= 3071' /etc/ssh/moduli > /etc/ssh/moduli.tmp && mv /etc/ssh/moduli.tmp /etc/ssh/moduli\`
```
- In `/etc/ssh/sshd_config`, consider the following SSHD config parameters:
```
# Supported HostKey algorithms by order of preference.
HostKey /etc/ssh/ssh_host_ed25519_key
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_rsa_key

# Password based logins are disabled - only public key based logins are allowed.
AuthenticationMethods publickey

# LogLevel VERBOSE logs user's key fingerprint on login. Needed to have a clear audit track of which key was using to log in.
LogLevel VERBOSE

PermitRootLogin no

# Log sftp level file access (read/write/etc.) that would not be easily logged otherwise.
Subsystem sftp  /usr/lib/ssh/sftp-server -f AUTHPRIV -l INFO
```
- You should also consider which algorithms and key types you'd like to support. Mozilla recommends the following key types (more restrictive than the OpenSSH defaults):
```
KexAlgorithms curve25519-sha256@libssh.org,ecdh-sha2-nistp521,ecdh-sha2-nistp384,ecdh-sha2-nistp256,diffie-hellman-group-exchange-sha256
Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com,aes128-gcm@openssh.com,aes256-ctr,aes192-ctr,aes128-ctr
MACs hmac-sha2-512-etm@openssh.com,hmac-sha2-256-etm@openssh.com,umac-128-etm@openssh.com,hmac-sha2-512,hmac-sha2-256,umac-128@openssh.com
```

On top of Mozilla's recommendations (which only cover up to OpenSSH 6.7), here are some things you can do to beef up your SSHD security:

- **Require a security key**: You can set up SSHD to only accept keys that use FIDO U2F security tokens.

```
PubkeyAcceptedKeyTypes sk-ecdsa-sha2-nistp256-cert-v01@openssh.com,sk-ssh-ed25519-cert-v01@openssh.com,sk-ecdsa-sha2-nistp256@openssh.com
```

> **Note:** This will prevent future connections to the host using the original PEM key you got upon launch. So, you'll need to generate a new `-sk` type key for the `ubuntu` account. [We wrote instructions for that here](https://smallstep.com/blog/ssh-tricks-and-tips/#add-a-second-factor-to-your-ssh).

> **Note:** This requires all of your clients to have OpenSSH 8.2+. If you do not want to restrict access by IP address in your security group rules, consider some additional hardening:
- Change your default SSH port. This will deter a lot of basic bots.

```
Port 37271
```
- [Set up port knocking](https://help.ubuntu.com/community/PortKnocking) Port knocking will complicate the task of connecting to the bastion, but it could be a good option if you need your bastion to be available to any IP address.
- Install [intrusion detection](https://en.wikipedia.org/wiki/Host-based_intrusion_detection_system_comparison) and prevention software

##### Disable Forwarding

Since we're not allowing shell access, we also want to prohibit all forwarding except TCP forwarding, which `ssh -J` uses to support bastions.

```
AllowAgentForwarding no
AllowStreamLocalForwarding no
X11Forwarding no
```

If all you need is an SSH gateway, you can disable shell access on the bastion itself.

```
Match User *,!ubuntu
        ForceCommand /bin/echo 'This bastion does not support interactive commands.'
```

By default, SSHD's TCP port forwarding will allow the user to forward their connection to any remote TCP port in your private network. You can limit forwarding to port 22 (SSH) if you don't want other kinds of traffic to be forwarded to internal hosts:

```
PermitOpen *:22
```
### Restart SSHD (and cross your fingers)

You can test your configuration with `sshd -t`, then restart the SSHD server. Make sure you can still `ssh` into the machine before you continue! 😱

### Send your users SSH keys to the bastion

- If you only have a few users, you can create a single account on the bastion that everyone will use, and make sure all of their public keys are added to it.
- If you have lots of users, use [Smallstep SSH](https://smallstep.com/sso-ssh/) and issue short-lived SSH certificates.

### Send logs to the cloud

You can [set up the AWS CloudWatch agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/QuickStartEC2Instance.html) or the Google [Cloud Logging Agent](https://cloud.google.com/logging/docs/agent), so that your SSH logs in particular will go to the cloud. With this in place, you can set up alerts for suspicious SSH activity.

### Allow emergency root access

See our [SSH Emergency Access](https://smallstep.com/blog/ssh-emergency-access) guide for a safe approach that allows emergency access to the host. Once you have set up emergency access keys, you can disable any other option for root access—since no one will regularly have a reason to use the `root` account on this machine.

### Set up your internal hosts to only allow SSH access from the bastion

This is an important Zero Trust policy: Any internal host you connect to should only allow SSH connections from the bastion. The easiest way to implement this is with an inbound firewall rule on those hosts.

### Configure your SSH clients.

Your clients should accept new host keys offered by a known host, making host key rotation a lot easier. (This will be the default in a future OpenSSH.)

```
UpdateHostKeys yes
```

And, you can add a configuration directive to make it easier to reach your internal hosts via the bastion. Let's say all of your internal hosts have names in the .internal domain, as is the case on AWS. You can use this directive to reach them:

```
Host *.internal
  ProxyJump bastion.example.com
```

Then, just `ssh host.internal` to connect to an internal host via the bastion. One subtle note here: The internal hostname will be resolved via DNS lookup on the bastion, not by your local machine. So as long as the bastion knows how to look up your internal hosts by their internal names and IPs, that's all you need.

Carl Tashian ([Website](https://tashian.com/), [LinkedIn](https://www.linkedin.com/in/tashian/)) is an engineer, writer, exec coach, and startup all-rounder. He's currently an Offroad Engineer at Smallstep. He co-founded and built the engineering team at Trove, and he wrote the code that opens your Zipcar. He lives in San Francisco with his wife Siobhan and he loves to play the modular synthesizer 🎛️🎚️