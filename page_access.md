---
layout: page
title: Access TravisTorrent
subtitle: Free and Open Travis Analytics for Everyone
comments: true
---

As a courtesy to our users, TravisTorrent provides both offline and online
access options. No registration or subscription is required. Users are kindly asked to follow a fair-use policy of server resources.

<div class="get-started-wrap"><a class="btn btn-success btn-lg get-started-btn" href="https://bigquery.cloud.google.com/table/travistorrent-bq:data.2017_01_11?tab=schema">Live Online Access</a> <a class="btn btn-success btn-lg get-started-btn" href="/dumps/travistorrent_11_1_2017.sql.gz">Download SQL Dump</a></div>
<br>

### Live Online Access
Thanks to Google, we can [provide a BigQuery interface](https://bigquery.cloud.google.com/table/travistorrent-bq:data.2017_01_11?tab=schema) to TravisTorrent. Everyone gets a free monthly Terabyte on BigQuery to instantly query it!

Usage Instructions: After following the above link, please login and authorize via your Google account (there is a free $300 voucher to get started and you require a credit card, but according to Google this is never charged unless explicitly asked for). If you are not already in the query browser, click on the big red button on the top left that says `Compose Query`. You can
then input sample queries in SQL syntax and run them via `CTRL+ENTER.  For example,
``SELECT AVG(tr_duration) FROM [travistorrent-bq:data.2017_01_11]`` computes the average mean build time over all projects. 

### Offline Access
We distribute snapshots of our database as compressed SQL and CSV files. The SQL is generated from automatically reading in the CSV.

Tip: If you use R and CSV, loading the CSV via `fread` from `data.table` offers a significant speed-up over `read.csv`.

|Snapshot Revision|Date|Size|
|[travistorrent_11_1_2017.sql.gz](/dumps/travistorrent_11_1_2017.sql.gz)|11.1.2017|193M|
|[travistorrent_11_1_2017.csv.gz](/dumps/travistorrent_11_1_2017.csv.gz)|11.1.2017|190M|
|archived: [travistorrent_6_12_2016.sql.gz](/dumps/travistorrent_6_12_2016.sql.gz)|6.12.2016|178M|
|archived: [travistorrent_6_12_2016.csv.gz](/dumps/travistorrent_6_12_2016.csv.gz)|6.12.2016|175M|
|archived: [travistorrent_27_10_2016.sql.gz](/dumps/travistorrent_27_10_2016.sql.gz)|27.10.2016|78M|
|archived: [travistorrent_27_10_2016.csv.gz](/dumps/travistorrent_27_10_2016.csv.gz)|27.10.2016|78M|
|archived: [travistorrent_30_9_2016.sql.gz](/dumps/travistorrent_30_9_2016.sql.gz)|30.9.2016|78M|
|archived: [travistorrent_30_9_2016.csv.gz](/dumps/travistorrent_30_9_2016.csv.gz) <br><sub>Includes changes to data types, e.g. TRUE instead of "true"</sub>|30.9.2016|79M|
|archived: [travistorrent_7_9_2016.sql.gz](/dumps/travistorrent_7_9_2016.sql.gz)|7.9.2016|78M|
|archived: [travistorrent_7_9_2016.csv.gz](/dumps/travistorrent_7_9_2016.csv.gz)|7.9.2016|79M|
|archived: [travistorrent_5_3_2016.sql.gz](/dumps/travistorrent_5_3_2016.sql.gz)|5.3.2016|51M|
|archived: [travistorrent_5_3_2016.csv.gz](/dumps/travistorrent_5_3_2016.csv.gz)|5.3.2016|79M|
|archived: [travistorrent_28_8_2015.csv.gz](/dumps/travistorrent_28_8_2015.csv.gz)|28.8.2015|65M|

### Raw Build Data
To allow a deeper analysis of project build logs, the 780 GB of build logs that our build log analysis (most entries prefixed with `tr_`) depends on can be retrieved, too. Please be gentle with server bandwidth. [Access the raw build logs](/buildlogs). If you used these, please also [cite or reference TravisTorrent properly](/#cite).
