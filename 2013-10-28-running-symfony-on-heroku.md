---
title: Running Symfony on Heroku
categories: [php, cloud, symfony]
tags: [php, cloud, heroku, buildpack]
published: true
---

If you're a PHP-only developer, odds are that you've never heard of [Heroku](http://heroku.com). It's one of the popular SAAS providers out there, but was targeting Ruby applications in the beginning. Nowadays they support almost everything with help of so-called [buildpacks](https://devcenter.heroku.com/articles/buildpacks). They currently don't have an official PHP buildpack (yet), but thanks to [Christoph Hochstrasser](http://christophh.net) you can deploy your PHP applications to Heroku.
<!-- more -->

The very first step is to install the [Heroku toolbelt](https://toolbelt.heroku.com/) so we can access our Heroku account through the command-line. Please make sure that you can do the following;

```bash
# Will output something like 'heroku-toolbelt/3.0.1 (x86_64-darwin10.8.0) ruby/1.9.3'
$ heroku --version
```

Now we need a Symfony application that we can deploy. Create a Symfony application somewhere on your computer with [Composer](http://getcomposer.org);

```bash
$ php composer.phar create-project symfony/framework-standard-edition myapp/ 2.3.6
```

It creates an application in the `myapp/` directory and installs all required dependencies. Now move into the directory when the installation is completed and modify _composer.json_ to configure the buildpack. We need to tell which _document-root_ we would like to use and set some PHP settings. Append the following to the _extras_ section of your _composer.json_ file;

```json
{
    "extra": {
        "heroku": {
            "document-root": "web",
            "php-config": [
                "date.timezone=Europe/Amsterdam",
                "display_errors=off",
                "short_open_tag=off"
            ]
        }
    }
}
```

Every Heroku application has a Git repository as base. Every time you push a new commit to the Heroku remote, it's automatically deployed on the platform as new version of your application (so you can also rollback to previously pushed commits). Before we can deploy our created Symfony application on Heroku, we need to initialized a git repository in our application directory and commit all application-related files.

```bash
# Run this commands from your application directory.
$ git init
$ git add .
$ git commit -m "Initialized a base Symfony application"
```

Our Symfony application is now ready to be deployed on the Heroku platform. As told before, we are going to use Christoph's [PHP buildpack](https://github.com/CHH/heroku-buildpack-php) as buildpack for this. Create an application with his buildpack on the CLI with the following command;

```bash
heroku create myapp --buildpack https://github.com/CHH/heroku-buildpack-php
```

It will automatically create an application on your Heroku account with the PHP buildpack. And as you can see in the command output, it added a 'heroku' remote to our git repository. Now push our commit to the remote repository;

```bash
$ git push heroku master
```

As you can see, a git hook is fired when Heroku receives the commit and it deploys the application through the buildpack. When the deployment is finished, you can navigate to your application and you would see an 404 error. This is because the standard edition of Symfony only includes the _AcmeDemoBundle_. And that bundle is only visible from the dev environment (app_dev.php).

A few things to note though. At first the use of _parameters.yml_ on the Heroku platform. In the standard edition of Symfony it is generated through a Composer hook and asks for some user input when it's generated for the first time. But in the Heroku deployment process, you can't provide this input. A better idea is to exclude _parameters.yml_ from _config.yml_ and add all parameters as environment variables. Symfony detect these variables when you prefix them with `SYMFONY__`. To set the _secret_ variable for example on Heroku, you can run the following command;

```bash
$ heroku config:set SYMFONY__SECRET=ThisTokenIsNotSoSecretChangeIt
```

Another thing to notice is the use of environment variables in the compile process. To make them available, you have to enable the [user-env-compile](https://devcenter.heroku.com/articles/labs-user-env-compile) labs feature;

```bash
$ heroku labs:enable user-env-compile -a myapp
```

A last thing is that you can enable [New Relic](https://newrelic.com) support every easily, by adding the following to your _composer.json_ file;

```json
{
    "extra": {
        "heroku": {
            "newrelic": true
        }
    }
}
```

Questions? Feel free to asked them by leaving a comment!
