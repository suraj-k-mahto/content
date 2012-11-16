---
type: RvdvBlogBundle:Article
title: Install the Mongo PHP extension in MAMP with PECL
categories: [mac, php]
tags: [extension, mac, mamp, mamp pro, mongodb, php]
---

Maybe as you guessed from [my previous post](/blog/upgrade-mamps-phpmyadmin), I develop on a Mac with MAMP. I really like the interface of the pro version compared to other programs like XAMPP. But it seems very difficult to install PEAR extensions with it. This post will explain how to install the [MongoDB extension](http://php.net/manual/en/class.mongodb.php), but I guess this technique can be applied to a lot of other extensions too.
<!-- more -->

1.  At first, download PHP's source code that matches the version you're using with MAMP from PHP.net. I am using 5.3.6, so I downloaded [this package](http://www.php.net/get/php-5.3.6.tar.bz2/from/a/mirror).
2.  Extract the contents of it to the following folder: ``/Applications/MAMP/bin/php/php5.3.6/include/php``
3.  Run the following command in this folder:
    ``./configure``
4.  The PEAR conf file causes some weird issues in MAMP, so we better remove it:
    ``rm /Applications/MAMP/bin/php/php5.3.6/conf/pear.conf``
5.  Now install the MongoDB extension with help of PECL
    ``cd /Applications/MAMP/bin/php/php5.3.6/bin``
    ``./pecl install mongo``
6.  Activate the installed extension in your php.ini file:
    ``extension=mongo.so``

And that's it. You can now use a MongoDB server in PHP applications. Most of the credits of the above steps go to [Matthias Schmidt](http://m-schmidt.eu/2011/11/06/develop-mongodb-web-apps-with-mamp-under-mac-os-x/).