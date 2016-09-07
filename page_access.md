---
layout: page
title: Access TravisTorrent
subtitle: Free and Open Travis Analytics for Everyone
comments: true
---

As a courtesy to our users, TravisTorrent provides both offline and online
access options. No registration or subscription is required. Users are kindly asked to follow a fair-use policy of server resources.

<div class="get-started-wrap"><a class="btn btn-success btn-lg get-started-btn" href="/dblite/">Live Online Access</a> <a class="btn btn-success btn-lg get-started-btn" href="/dumps/travistorrent_7_9_2016.sql.gz">Download SQL Dump</a></div>
<br>

### Live Online Access
We provide a MySQL web client interface [DBLite instance](/dblite/) that
has the latest TravisTorrent snapshot loaded into it.

Usage Instructions: After following the above link, please click on `Continue
as Guest` to access the web interface.  Select database `travist`, under which
you will find the TravisTorrent timestamped tables. Select one of them. You can
then input sample queries and run them via the blue arrow.  For example,
``SELECT AVG(purebuildduration) FROM travistorrent_7_9_2016`` computes the
average mean of the build time over all projects.

### Offline Access
We distribute snapshots of our database as compressed SQL and CSV files (uncompressed 1.8 GB). The SQL is generated from automatically reading in the CSV.

Tips: You can import the SQL simply via `mysql -u root -p your_database < travistorrent_7_9_2016.sql`. If you use R and CSV, you probably want to load the CSV via `fread` from `data.table`.

|Snapshot Revision|Date|Size|
|[travistorrent_7_9_2016.sql.gz](/dumps/travistorrent_7_9_2016.sql.gz)|7.9.2016|78M
|[travistorrent_7_9_2016.csv.gz](/dumps/travistorrent_7_9_2016.csv.gz)|7.9.2016|79M
|archived: [travistorrent_5_3_2016.sql.gz](/dumps/travistorrent_5_3_2016.sql.gz)|5.3.2016|51M|
|archived: [travistorrent_5_3_2016.csv.gz](/dumps/travistorrent_5_3_2016.csv.gz)|5.3.2016|79M|
|archived: [travistorrent_28_8_2015.csv.gz](/dumps/travistorrent_28_8_2015.csv.gz)|28.8.2015|65M|

### Raw Build Data
To allow a deeper analysis of project build logs, the 780 GB of build logs that our build log analysis (most entries prefixed with `tr_`) depends on can be retrieved, too. Please be gentle with server bandwidth. [Access the raw build logs](/buildlogs). If you used these, please also [cite or reference TravisTorrent properly](/#cite).
