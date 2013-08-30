---
title: Mock Guzzle responses in Symfony tests
categories: [php, symfony, test]
tags: [php, symfony, test, mock, guzzle]
published: true
---

Most of the web applications out there have at least one integration with an external service. Like with Twitter for pulling in tweets for example. On the other hand, most of the web applications have (or should have) functional testing setup to be less error prone when deploying new releases. You want these tests to be executed as quick as possible and test every possible scenario that could happen with your external service integration.

I had the same with my Twitter integration (the latest tweet bar on top of this site). These are fetched with the brilliant [Guzzle library](http://guzzlephp.org) and luckily they came up with functionality to mock up responses. I'll show you how to do it in Symfony.
<!-- more -->

They wrote a good explanation about mocking responses at their [docs](http://guzzlephp.org/testing/unit-testing.html#queueing-mock-responses). You'll have to add the `Guzzle\Plugin\Mock\MockPlugin` as subscriber to your client object. But in Symfony this client is probably injected as a service. So grab this service and add the plugin before executing any of your tests:

```php
<?php

use Guzzle\Http\Message\Response;
use Guzzle\Plugin\Mock\MockPlugin;

class HeaderControllerTest extends WebTestCase
{
    public function testTweets()
    {
        $client = static::createClient();

        $twitterClient = $client->getContainer()->get('rvdv_blog.guzzle.twitter_client');

        $plugin = new MockPlugin();
        $plugin->addResponse(__DIR__.'/twitter_200_response.txt');
        $twitterClient->addSubscriber($plugin);

        $crawler = $client->request('GET', '/_header_tweets');
        $response = $client->getResponse();

        $this->assertTrue($response->isSuccessful());
        $this->assertTrue($response->isCacheable());
        $this->assertEquals(3600, $response->getMaxAge());
        $this->assertNotNull($response->getEtag());

        $this->assertGreaterThan(0, $crawler->filter('li')->count());
    }
}
```

As you can see, the client is grabbed from the DI container and the mock plugin is added as subscriber. But what's that text file doing there? It contains the raw response as you would actually receive from your service. Like for example:

```bash
HTTP/1.1 200 OK
Date: Wed, 25 Nov 2009 12:00:00 GMT
Connection: close
Server: AmazonS3
Content-Type: application/xml

<?xml version="1.0" encoding="UTF-8"?>
<LocationConstraint xmlns="http://s3.amazonaws.com/doc/2006-03-01/">EU</LocationConstraint>
```

You can create multiple raw responses for defining multiple scenarios of your webservice integration. Maybe you want to test a 500 response or a 401 and check if your application can handle these correctly.

But how to get these raw responses from your webservice? I would recommend a handy tool for that called [Charles](http://www.charlesproxy.com). It's a HTTP proxy that sits between your local environment and your webservice. Turn Charles on and add the following CURL options to your Guzzle client to see all traffic pass by:

```php
<?php

use Guzzle\Http\Client;

$client = new Client('https://api.twitter.com/{version}', array(
    'version'        => 'v1.1',
    'request.options' => array(
        // This should be the port you've configured Charles to listen for.
        'proxy'   => 'http://localhost:8888',
    )
));
```

When you've grabbed all the responses with Charles, you can remove the proxy configuration. Happy testing!
