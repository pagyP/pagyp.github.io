---
title: "SYSVOL Not Replicating - The content set is not ready"
date: "2016-01-12"
categories: 
  - "activedirectory"
  - "sysvol"
tags: 
  - "replication"
---

Had an odd problem in a lab environment.  The lab was only two Windows 2012R2 core domain controllers, fully patched and up to date and a WSUS server.  For some reason SYSVOL was not replicating and I only noticed when I configured a GPO for WSUS and noticed that one of the DCs never registered in the WSUS console.

AD replication was fine - a repadmin /replsum did not show any errors.  Looking in the DFS Replication log did not show any errors on either DC but what was not logged was an event ID 4604.  A 4604 is an indication that SYSVOL replication has been enabled and the initial sync completed.

Looking in the DFSR logs in c:windowsdebug showed;

 

20160112 20:18:23.416 3088 ISYN    68 InitialSyncManager::ReturnToken InitialSync sync step not finished yet. Wake up other session tasks. 20160112 20:18:43.127 1824 SRTR   971 \[WARN\] SERVER\_EstablishSession Failed to establish a replicated folder session. connId:{EC12E981-AFF0-4B07-85F6-B3FA698EDA9E} csId:{33FC6089-F773-4647-A039-0D3EA525329B} Error: + \[Error:9051(0x235b) UpstreamTransport::EstablishSession upstreamtransport.cpp:803 1824 C The content set is not ready\] + \[Error:9051(0x235b) OutConnection::TransportEstablishSession outconnection.cpp:510 1824 C The content set is not ready\] + \[Error:9051(0x235b) OutConnection::TransportEstablishSession outconnection.cpp:449 1824 C The content set is not ready\]

As the DCs run server core the DFS Management tools (including dfsrdiag) are not available.  I installed the DFS Management tools on a server with a GUI - right click the top branch in the left hand side of the MMC console where it says DFS Management and select 'Add Replication Groups to Display'.  Add the Domain System Volume.

Running a health report showed that DC01 was waiting for initial sync and DC02 also waiting for initial sync but with some backlogged files.

DC01 was the up to date DC as I could see more policy objects in SYSVOL on that server.

To fix this I ran up adsiedit and went to;

Default naming context | Domain Controllers OU | DC01 | DFSR-LocalSettings | Domain System Volume | SysVol Subscription | Properties

and set the below property.  It was set to 0.

- Set DFSR-Options to 1

Within 5 minutes I then had an event ID 4604 logged on both DCs and SYSVOL replicating problems

This was a shorter version of https://support.microsoft.com/en-us/kb/2218556 (How to force an authoritative and non-authoritative synchronization for DFSR-replicated SYSVOL)

Whether not following the full procedure has any impact in the future I'm not sure but as it was a lab environment with only a few servers it didn't matter too much and it did fix my SYSVOl replication problems.
