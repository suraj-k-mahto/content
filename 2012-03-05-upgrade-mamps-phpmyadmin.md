---
title: Upgrade MAMP's phpMyAdmin
categories: [mac, php]
tags: [environment, mac, mamp, mamp pro, mysql, php, phpmyadmin, upgrade]
published: true
---

My current [MAMP](http://www.mamp.info) version ships with phpMyAdmin version 3.3.9.2. But there is already a higher version available, version 3.4.10.1. Upgrading the shipped phpMyAdmin to the latest version appears to be very easy. Just downloading and extracting it to the correct folder.
<!-- more -->

At first, download the latest available version from phpMyAdmin's [download page](http://www.phpmyadmin.net/home_page/downloads.php).
If you have MAMP, extract the files to the following directory (backup the old files first just in case): `/Applications/MAMP/bin/phpMyAdmin`.
If you restart MAMP and go to `http://localhost/phpMyAdmin`, you'll see that your phpMyAdmin interface is upgraded.

For everybody who has MAMP Pro, the correct location of your phpMyAdmin files is `/Library/Application Support/appsolute/MAMP PRO/phpMyAdmin`.
