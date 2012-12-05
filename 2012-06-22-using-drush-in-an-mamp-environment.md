---
type: RvdvBlogBundle:Article
title: Using Drush in a MAMP environment
categories: [mac, drupal]
tags: [drupal, drush, mac, mamp, mamp pro, php]
---

[Drush](http://drush.ws/) is a very powerful instrument in the toolbox of a Drupal developer. But it can give you a little headache when trying to install in your local MAMP setup.
Also because I had forgotten how to do it within my fresh installation of MAMP Pro, a quick and relatively simple tutorial on how to install Drush.
<!-- more -->

## Make MAMP's PHP binary the default one

When you install MAMP on your Mac, there are two PHP versions installed on your machine. Mac ships with a default Apache/PHP setup and will be the one that is used by default. You need to tell the command-line to use the MAMP's PHP rather than the one that ships by default with your Mac.

You'll do that by overriding the path variable. Type the following in the terminal;
``echo $PATH``
The output of the Terminal should be similar to this:
``/sbin:/usr/local/bin:/usr/X11/bin``

MAMP Pro comes with two versions of PHP; *5.2.17* and *5.3.6*. We will make version *5.3.6* the default one in our terminal. To override the PATH variable, you need to edit the *~/.bash_profile* file and add our PATH variable to it. Type the following in the terminal to edit this file:
``sudo vi ~/.bash_profile``
This will open the VIM editor and lets you edit the file. Make sure the following line exists and otherwise enter it:
``PATH=/Applications/MAMP/Library/bin:/Applications/MAMP/bin/php/php5.3.6/bin:$PATH``
With this line, we tell the system to use the PHP binary in the 5.3.6 folder, before any other existing PHP binaries on the system.

When you now run ``which php`` in the Terminal, you'll see something similar to the following and we can continue installing Drush;
``/Applications/MAMP/Library/bin``

## Installing Drush through PEAR
MAMP's PEAR comes with a conf file which causes weird issues. Luckily I had used PEAR [earlier](/blog/install-the-mongo-php-extension-in-mamp-with-pecl) and fixed this by running the following command:
``rm /Applications/MAMP/bin/php/php5.3.6/conf/pear.conf``

Now you have to make sure that you've installed the *console_getopt* PEAR module, so run the following in your terminal;
``pear install Console_Getopt``

You're now all set to install Drush, run the following commands to do this;

1.  ``pear upgrade --force Console_Getopt``
2.  ``pear upgrade --force pear``
3.  ``pear upgrade-all``
4.  ``pear channel-discover pear.drush.org``
5.  ``pear install drush/drush``

To check if everything works correctly, run the last command of this tutorial in your terminal:
``drush version``
If everything works fine, you'll see *drush version 5.4* as output.
