---
layout: post
title:  "Proxmox node failure, how to fix a Proxmox cluster"
date:   2025-04-24 15:30:24 +0000
categories: selfhosting
---

I had a Proxmox node fail on me recently.  It was a Dell Optiplex 7010 with 32GB RAM and a 240GB SSD.  The node was part of a Proxmox cluster with two other nodes.  Not entirely sure what happened but it looks like the power supply failed, unfortunatley at the moment I can't afford to replace either the power supply or the node itself.  So I needed to remove the node from the cluster and reconfigure the cluster to run on just two nodes.  This is a pretty simple process but I thought it would be worth documenting in case anyone else finds themselves in a similar situation.    

## Fixing the cluster


First check the status of the cluster:

```bash
root@pve1:~# pvecm nodes

Membership information
----------------------
Nodeid Votes Name
3 1 pve3
4 1 pve1 (local)
```

- I expected the dead node to be listed here as well.  In my case it was not, but I could see that the node was still listed in the cluster configuration in the web gui.

- In addition I expcected to see the number of quorum votes for the cluster to be 2, but it still said 3:

```bash
Votequorum information
----------------------
Expected votes: 3
Highest expected: 3
Total votes: 2
Quorum: 2
Flags: Quorate 
```


To fix this up I needed to run the following command on one of the other nodes in the cluster:

- Remove the dead node from /etc/pve/corosync.conf on any node in the cluster.
- systemctl restart pve-cluster
- systemctl restart corosync
- That removed the node from the cluster and pvecm status then reports the correct number of votes (2)
- Then to remove the dead node from the web gui remove from /etc/pve/nodes/nodename - just remove the whole directory

References:
- https://forum.proxmox.com/threads/node-failure.165179/#post-765580 