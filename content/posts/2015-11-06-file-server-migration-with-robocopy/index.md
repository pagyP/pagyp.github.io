---
title: "File Server Migration with Robocopy"
date: "2015-11-06"
categories: 
  - "server"
  - "windows"
tags: 
  - "robocopy"
---

I'm in the process of migrating data from an aging file server to a new shiny one.  On the old server the NTFS permissions are all over the place so I really wanted to copy the data to the new server without the existing NTFS permissions and then apply new ACLs on the new server.

I decided on [Robocopy](https://technet.microsoft.com/en-us/library/cc733145.aspx) to copy the data.  The command line and switches I used are;

robocopy \\OldFileServershare \\NewFileServerNewShare /MIR /COPY:DATO /DCOPY:T /ZB /r:1 /w:1 /log:robo.log

/MIR mirrors the entire directory structure

/COPY:DATO copes the Data, Attributes, Timestamps, Owner information.  The NTFS permissions are not copied.  Using an S in the above would also copy the NTFS access control list.

/DCOPY:T - Copies directory timestamps

/ZB  - Use restart mode and if access to a file/directory is denied use the backup option (e.g. copy with backup privileges)

/r:1  - Specifies the number of retries on a failed item

/w:1 - Specifies the wait time between retries

/log Outputs the result of the copy to a log file

Using the /log file means you don't see anything happening in the cmd window whilst robocopy is working.  I like to use which is available as part of the [System Center 2012 R2 Configuration Manager Toolkit](https://www.microsoft.com/en-gb/download/details.aspx?id=36213)

CMTrace is a SCCM tool and really good for viewing log files in real time.  Opening robo.log with CMtrace means I can watch what Robocopy do its work whilst it is being output to a log file for me to view later.  This can be handy in case there were any failures or warnings.
