---
title: Install the INTL PHP extension in MAMP
categories: [mac, php]
tags: [extension, mac, mamp, mamp pro, intl, php]
published: true
---

As a Symfony developer you run into [translations](http://symfony.com/doc/current/book/translation.html) sooner or later. This is a very powerful component of the framework. But requires some additional configuration of your MAMP environment before you can start using it. I'll explain to you in short how.
<!-- more -->

First, grab the [ICU libraries](http://site.icu-project.org/download/48#ICU4C-Download) and build them:

```bash
$ curl -O http://download.icu-project.org/files/icu4c/4.8.1.1/icu4c-4_8_1_1-src.tgz
$ tar xzvf icu4c-4_8_1_1-src.tgz
$ ./runConfigureICU MacOSX
$ make && sudo make install
```

Now you have to install the PHP INTL extension with PECL. Make sure you've added the MAMP PHP binary directory to your $PATH environment variable, as described by this [previous post](/blog/using-drush-in-a-mamp-environment). Then run the following command to install the INTL extension:

```bash
$ pecl install intl
```

The last thing to do is add the `extension=intl.so` line to the php.ini file, so do that. Restart your shell and MAMP environment so they reflect the changes to the ini file.
