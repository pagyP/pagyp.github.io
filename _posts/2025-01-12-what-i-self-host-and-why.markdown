---
layout: post
title:  "What I self host and why and my home network setup"
date:   2025-01-12 15:30:24 +0000
categories: selfhosting
---

I selfhost a number services at home.  In this post I'll talk through what I self host and give some brief reasoning as to why that particualr service and what it replaces/complements.  
The TLDR is I self host mostly as an educational exercise, I love to figure out how to use different technologies and there is so much open source goodness in the world that you can pick almost anything to self host and learn.  In addition there is an strong element about maintaining control of my own data and not wholly relying on cloud based services from the big, well known, providers.

Couple of notes:

- I always try to use open source software when I self host a service
  
- I donate or contribute (if I have the requisite skills) to open source projects that I like and use

...
Before diving into the services/application I use I'll give an overview of my home network setup:

| VLAN No. | VLAN Purpose | VLAN CIDR |
|:---------|:-------------|----------:|
| VLAN1    | Native VLAN  | x.x.1.0/24 |
| VLAN6    | Guest SSID 2.4 Ghz | x.x.6.0/24 |
| VLAN7    | Home SSID 2.4 Ghz | x.x.7.0/24  |
| VLAN 8   | Home SSID 5 Ghz   | x.x.8.0/24  |
| VLAN 9   | Guest SSID 5Ghz   | x.x.x.9.0/24|
| VLAN 20  | Domain Controllers| x.x.20.0/24 |
| VLAN 30  | Windows Member Servers | x.x.30.0/24 |
| VLAN 50  | DMZ               | x.x.50.0/24 |

Perhaps somewhat obviously I don't need /24 subnets for each of these vlans but I like to keep things consistent and it makes it easier to remember the subnets, and it's not like I'm going to run out of address space in a home network.  I have a number of devices on each of these vlans, some of which I'll mention in the services I self host.

#### Hardware

- Protectli VP2420 <https://eu.protectli.com/product/vp2420/> running Pfsense CE <https://pfsense.org/>
  - All inter vlan routing happens via the Pfsense device.

- 8 port Draytek managed switch handles vlans at layer 2 <https://www.draytek.co.uk/products/business/vigorswitch-g1080>
- 3 Dell Optiplex 7010 small form factor devices running Proxmox, these form a Proxmox cluster to host my virtual machines
  - Each of the Optiplex 7010's has 32GB RAM and a 240GB SSD
- Asustor NAS device with 2 x 2TB drives in RAID 1.  All 2 TB are presented to the Proxmox cluster as SMB storage.  
- A Dell Poweredge T20 with 32GB RAM and 2 x 1TB in RAID0 runs Proxmox Backup Server and backs up all VMs and LXC containers to the local drives in this server.
- A MinisForum U850 Mini PC running Pop OS with 32GB RAM and a 512GB SSD.  


#### Wifi

I currently use a Zyxel AWX90 access point for 2.4Ghz and 5Ghz wifi.  Each SSID has client isolation enabled.

##### Firewall notes

- Suricata runs on Pfsense for Intruder Detection
- HAproxy runs on Pfsense for reverse proxying to services running on the Proxmox cluster
- ACME package runs on Pfsense to manage Lets Encrypt certificates for services running on the Proxmox cluster

...

#### Services


| Service       | Description               | Reason                             |
|:--------------|:--------------------------|-----------------------------------:|
| Jellyfin      | Stores and plays my music | To have control over my music collection |
| Nextcloud     | File/Photo storage and sharing  | To maintain control over my data   |
| Pi-Hole       | DNS and AD Blocking       | DNS and AD Blocking                |
| FreshRSS      | RSS Reader                | Subscribe to favourite RSS Feeds   |
| Calibre       | Ebook management          | Organise and read ebooks           |
| Cisco Modeliing Labs | Network simulation | Learning and labbing networking     |
| Proxmox VE     | Virtualisation           | Virtualisation Platform            |

...

#### What runs on what

- All pi-hole DNS servers run on LXC containers on the Proxmox cluster

- Nextcloud I use the Nextcloud All In One (AIO), which runs under Docker on a Ubuntu 22.04 VM on the Proxmox cluster

- FreshRSS runs on a LXC container on the Proxmox cluster

- Calibre runs on the MinisForum Mini PC

- Cisco Modelling Labs runs in a VM on the Proxmox cluster

- Jellyfin runs on the MinisForum Mini PC

##### Service Presentation

- Jellyfin is presented to the network via HAproxy on Pfsense
- Nextcloud is presented to the network via HAproxy on Pfsense
- FreshRSS is presented to the network via HAproxy on Pfsense
- Calibre is presented to the network via HAproxy on Pfsense
- Cisco Modelling Labs is presented to the network via HAproxy on Pfsense

What the above means is I can access these services by a domain name I own and have valid certificates for them via Lets Encrypt.
I don't publicly expose any of these services to the internet.  I use a VPN to access them when I'm not at home.

#### Why?

My initial idea was to self host my own data because of privacy concerrns using online services like Google Drive, One Drive etc.
I still have concerns about privacy but I also self host as an educational exercise.  I love to learn new technologies and there is so much open source goodness in the world that you can pick almost anything to self host and learn.  In addition there is an strong element about maintaining control of my own data and not wholly relying on cloud based services from the big, well known, providers.

#### Future projects and hardware

Some things I'm considering on adding to my self hosted services in the future:

- Graylog for log management
- Security Onion for network security monitoring
- Wazuh for host based intrusion detection
- Ansible for configuration management
- Replace the current 8 port switch with something more capable.  Probably something with 2.5 GB ports and more than 8 ports

Hopefully I'll find the time to write up more on the software, services and hardware I use in the future.

##### Does this help with my professional work life?

- Yes, I've been able to expand my knowledge about networking, virtualisation, Linux and Docker by self hosting services at home.  This has helped me in my professional life as I've been able to apply some of the knowledge I've gained to my work.
- I also run site to site VPNs to Azure (I mostly work with Azure) and have been able to test out different VPN configurations, for example
  - Policy based VPNs
  - Route based VPNs
  - Dynamic routing with BGP
  - Static routing
- From an Azure perspective this allows me to test things like private endpoints and how DNS resolution works in Azure when using private endpoints. And gives me a better understanding of how traffic flows within Azure and between Azure and on-premises.
