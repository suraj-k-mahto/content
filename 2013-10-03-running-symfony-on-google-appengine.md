---
title: Running Symfony on Google AppEngine
categories: [php, cloud, symfony]
tags: [php, cloud, google, appengine]
published: false
---

One of the major trends these days is to run your applications on one the PAAS providers like Heroku, AppFog, PagodaBox or Google AppEngine. Not many of them provide a solid solution for the PHP language, but this is likely going to change in the near future.
Google AppEngine has [PHP support](http://googlecloudplatform.blogspot.nl/2013/05/app-engine-adds-php-support.html) in beta for now and I am very exiting to check out how well it runs. So I'm gonna deploy one of my favorite frameworks at the moment, [Symfony](http://symfony.com).
<!-- more -->

The first step you'll have to take, is to have your account whitelisted for PHP applications. You can sign up for it [here](https://gaeforphp.appspot.com/). My account was whitelisted in a month and a half, so please be patient.

The second step is to install the AppEngine runtime environment (SDK) on your computer. Google has some very decent documents about [this topic](https://developers.google.com/appengine/docs/php/gettingstarted/installingmac). The only thing I needed to do was to move the _GoogleAppEngineLauncher_ to my Applications directory. But maybe I'll run into problems when using any of the PHP extensions mentioned by the installation doc.
Afterwards, I created an application by pressing the _Create a new application_ button. Selected PHP as runtime and entered an application ID. By pressing _run_, my application is now available under `http://localhost:8080`.

![screenshot of GoogleAppEngineLauncher](https://dl.dropboxusercontent.com/u/599492/robinvdvleuten/2013-10-03-running-symfony-on-google-appengine-1.png)

Hello World isn't that exiting, so we proceed with getting Symfony on the AppEngine SDK. I removed the directory created by SDK (in my case the _helloworld_ directory) and installed Symfony in a similar named directory with the following command;

```bash
$ php composer.phar create-project symfony/framework-standard-edition helloword/ 2.3.5
```

When you now try to go to `http://localhost:8080`, an error occurs because the AppEngine SDK doesn't know how to handle this application (we just deleted the config file for that in the previous step). Let's recreate one by creating an `app.yaml` file in root of the project with the following contents;

```yaml
application: helloworld
version: 1
runtime: php
api_version: 1

handlers:
- url: /config.php
  script: web/config.php
```

I only have a handler for the config script now, so we can check if everything is configured correctly for Symfony. But too bad it isn't. When navigating to `http://localhost:8080/config.php`, I've received the following error;

```bash
ContextErrorException: Warning: ini_set(): Cannot find save handler 'files' in
/Projects/helloworld/app/cache/dev/classes.php line 319
```

My first guess is that the SDK isn't using the correct PHP executable (I am using MAMP on my Macbook), so in my second attempt I'll run the SDK from the command-line with the correct path to PHP;

```bash
dev_appserver.py --php_executable_path=/Applications/MAMP/bin/php/php5.4.10/bin/php-cgi helloworld/
```

Because it is using MAMP's PHP executable, nothing SDK-specific was mentioned here. The only thing is that all requests other then `/config.php` aren't handled correctly. So we change `app.yaml` to handle all requests;

```yaml
application: helloworld
version: 1
runtime: php
api_version: 1

handlers:
- url: /bundles
  static_dir: web/bundles
- url: /config.php
  script: web/config.php
- url: /app.php
  script: web/app.php
- url: /app.php/(.+)?/?
  script: web/app.php
- url: /app_dev.php
  script: web/app_dev.php
- url: /app_dev.php/(.+)?/?
  script: web/app_dev.php
- url: /(.+)?/?
  script: web/app.php
```

I've added all development-related urls, but you can imagine that you want to remove those on a production environment.

Now that we have a running Symfony application on the local SDK, it's time to deploy it on the AppEngine. You can do this by running;

```bash
appcfg.py update helloworld/
```

It asks for your Google account credentials and uploads your application to the cloud...but it doesn't. Instead it gives some errors about invalid characters in cached files. We need to make the AppEngine ignore both `app/logs` an `app/cache` directories.
There seems to be a way to [skip files](https://developers.google.com/appengine/docs/php/config/appconfig#PHP_app_yaml_Skipping_files), so lets add this to our `app.yaml` file.

```yaml
skip_files:
- ^app/logs/.*$
- ^app/cache/.*$
```

The only problem with this, is that we still need writable access to both directories...
