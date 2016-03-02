---
layout: page
title: Access TravisTorrent
subtitle: Free and Open Travis Analytics for Everyone
comments: true
---

As a courtesy to our users, TravisTorrent provides both offline and online
access options. No registration or subscription is required. Users are kindly asked to follow a fair-use policy of server resources.

<div class="get-started-wrap"><a class="btn btn-success btn-lg get-started-btn" href="http://travistorrent.testroots.org/dblite/">Live Online Access</a> <a class="btn btn-success btn-lg get-started-btn" href="">Download CSV Dump</a></div>
<br>

### Live Online Access
We provide a MySQL web client interface [DBLite instance](http://travistorrent.testroots.org/dblite/) that
has the latest TravisTorrent snapshot loaded into it.

Usage Instructions: After following the above link, please click on `Continue as Guest` to access the web interface.
Select database `travist`, under which you will find the TravisTorrent timestamped tables. You can then input sample queries and run them via the blue arrow. 
For example, ``SELECT AVG(purebuildduration) FROM `travistorrent-28-8-2015` `` computes the average mean of the build time over all projects.

### Offline Access
We distribute snapshots of our database as a [CSV file (4.3.2016, 1.3 GB)](http://testroots.org/travistorrentdumps/travistorrent-28-8-2015.csv).

Tip: If you use R, you probably want to load it via `fread` from `data.table`.

|Snapshot Revision|Date|
|[travistorrent-2.csv]()|1.3.2016|
|[travistorrent-1.csv](http://testroots.org/travistorrentdumps/travistorrent-28-8-2015.csv)|28.8.2015|
