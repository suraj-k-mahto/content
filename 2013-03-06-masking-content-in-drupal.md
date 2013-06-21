---
title: Masking Content In Drupal
categories: [php, drupal]
tags: [drupal, content, masking, module]
published: true
---

Today I released a very useful [module](http://drupal.org/project/content_mask) for masking parts of your content for certain roles, users or even organic groups. It is based on Wordpress's [shortcode API](http://codex.wordpress.org/Shortcode_API) and must be used in combination with a text format in Drupal.
<!-- more -->

A very simple example looks like the following;

```
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla ac ligula
non augue scelerisque dapibus vel et ante. Pellentesque volutpat nibh ut
ipsum bibendum euismod. Ut nibh nisi, aliquam sed ornare eu, hendrerit
quis nunc. Nam vel nunc sed erat dapibus accumsan id eget nunc.

[content_mask uid="1"]
Mauris ornare augue ac augue tempor auctor. Praesent justo ligula,
convallis quis semper a, eleifend ut mi. Etiam eleifend aliquet
quam, accumsan eleifend erat vestibulum at. Pellentesque habitant
morbi tristique senectus et netus et malesuada fames ac turpis egestas.
[/content_mask]

[content_mask role="editor|administrator"]
Praesent rutrum, ligula vitae porta faucibus, nunc ante consequat est,
in imperdiet eros odio vitae nunc.
[/content_mask]
```

As you can see, you can mask content based on the user's UID or role. Please note that the attribute you configure on the shortcode is for users who are authorized to view the content.

The module's project page can you find [here](http://drupal.org/project/content_mask). If you have any suggestions for the module or have some cool use cases, let me know!
