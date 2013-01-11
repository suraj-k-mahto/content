---
title: Remove Drupal core stylesheets with hook_css_alter
categories: [drupal, php]
tags: [css, drupal, php, stylesheets, theming]
---

When you start developing themes, you'll find out that there will be a lot of stylesheets loaded in by default. To remove those stylesheets from your own theme's *template.php*, you have to overwrite *hook_css_alter* and remove the stylesheets from the array. To make things a bit clearer, take a look at the following snippet:
<!-- more -->

~~~

<?php
/**
 * Remove Drupal's core system stylesheets.
 */
function THEMENAME_css_alter(&$css)
{
    $path_system = drupal_get_path('module', 'system');

    $remove = array(
        $path_system . '/system.base.css',
        $path_system . '/system.menus.css',
        $path_system . '/system.messages.css',
        $path_system . '/system.theme.css',
    );

    // Remove stylesheets which match our remove array.
    foreach ($css as $stylesheet => $options) {
        if (in_array($stylesheet, $remove)) {
            unset($css[$stylesheet]);
        }
    }
}

~~~

As you can see, it removes all system module stylesheets from the stylesheets that will be rendered. This can be of course any stylesheet of any module you want.
