---
title: Handle authenticated users in Behat/Mink
categories: [php, symfony, bdd, behat, mink, authentication]
tags: [php, symfony, bdd, behat, mink, authentication]
published: true
---

One of the latest addition to PHP's testing tools is [Behat](http://behat.org). A test framework that focusses on business expectations (behavior driven development) versus the good ol' unit tests of [PHPUnit](http://phpunit.de) (test driven development). In short, with BDD you are describing your functionality instead of testing your functionality with TDD afterwards.
If you use Behat in combination with [Mink](http://mink.behat.org), you can test the behaviour of your application in the browser. It simulates the interaction between users and your application. But what if you have any authentication mechanism in your application, do you need to go through the login form in any test? The answer is no. And in this article I'll explain how.
<!-- more -->

Most of the apps these days have some sort of authentication mechanism. Maybe it's an ordinary login form, or users can be authenticated through an external oAuth service like Facebook or Twitter. You definitely want to test this functionality thoroughly and it can be easily done through Behat/Mink. So first take a look at the following Behat feature;

```yaml
Feature: Sign in to the website
    In order to access the administrative interface
    As a visitor
    I need to be able to log in to the website

    Background:
        Given there are following users:
            | username | email       | plain_password | enabled |
            | bar      | bar@foo.com | foo            | yes     |

    Scenario: Log in with username and password
        Given I am on "/admin/login"
         When I fill in the following:
            | username | bar |
            | password | foo |
          And I press "Login"
         Then I should be on "/admin/"
          And I should see "Logout"

    Scenario: Log in with bad credentials
        Given I am on "/admin/login"
         When I fill in the following:
            | username | bar@foo.com |
            | password | bar         |
          And I press "Login"
         Then I should be on "/admin/login"
          And I should see "Invalid username or password"
```

It's a very simple feature describing the usage of a login form. At first, it creates a `bar` user and then tries to login with correct and incorrect credentials and checks the response.
But what if you need to write a feature that describes the usage of for example an admin dashboard. Such feature would look like the following;

```yaml
Feature: Use the admin dashboard
    In order to see the current status of my application
    As an administrative user
    I need to be able to use the admin dashboard

    Background:
        Given there are following users:
            | username | email       | plain_password | enabled |
            | bar      | bar@foo.com | foo            | yes     |

    Scenario: Displaying the blog overview
        Given I am on "/admin/login"
         When I fill in the following:
            | username | bar |
            | password | foo |
          And I press "Login"
         Then I should be on "/admin/"
          And I should see "Admin dashboard"
```

As you can see, you need to fill in the login form in order to view the admin dashboard. But haven't we just tested this functionality in a previous version? Maybe you can imagine yourself writing the same logic over and over again for each scenario where you need an authenticated user.

I wasn't very happy with it and thought that there would be a solution that could be reused instead of writing the login process for each scenario independently. After a bit of googling, I found out that the [guys at KNP Labs](http://knplabs.com/blog/2011/12/15/behat-like-a-boss-meta-steps/) were struggling with the same problem. Their solution grouped the steps involving the login process into one step with so-called 'meta-steps'.
It is a much cleaner solution regarding the amount of lines in the features, but you still need to go through the login process every time you need to test the behavior of the administrative sections.

There has to be a better option and after googling a bit more, I stumbled upon the Symfony cookbook where they explain [how authentication can be simulated through tokens](http://symfony.com/doc/current/cookbook/testing/simulating_authentication.html). This looks very promising and after some trail and error runs of Behat and Mink, I came up with the following code in my FeatureContext;

```php
<?php

use Behat\Mink\Driver\BrowserKitDriver;
use Symfony\Component\BrowserKit\Cookie;
use Symfony\Component\Security\Core\Authentication\Token\UsernamePasswordToken;

/**
 * @Given /^I am authenticated as "([^"]*)"$/
 */
public function iAmAuthenticatedAs($username)
{
    $driver = $this->getSession()->getDriver();
    if (!$driver instanceof BrowserKitDriver) {
        throw new UnsupportedDriverActionException('This step is only supported by the BrowserKitDriver');
    }

    $client = $driver->getClient();
    $client->getCookieJar()->set(new Cookie(session_name(), true));

    $session = $client->getContainer()->get('session');

    $user = $this->kernel->getContainer()->get('fos_user.user_manager')->findUserByUsername($username);
    $providerKey = $this->kernel->getContainer()->getParameter('fos_user.firewall_name');

    $token = new UsernamePasswordToken($user, null, $providerKey, $user->getRoles());
    $session->set('_security_'.$providerKey, serialize($token));
    $session->save();

    $cookie = new Cookie($session->getName(), $session->getId());
    $client->getCookieJar()->set($cookie);
}
```

When I now need an authenticated user in my scenario, I can just write the following;

```yaml
Scenario: Displaying the blog overview
    Given I am authenticated as "bar"
      And I am on "/admin/"
     Then I should see "Admin dashboard"
```

In my opinion a much better solution than just writing the login process over and over again.
