---
type: RvdvBlogBundle:Article
title: Add your own styles to Drupal's WYSIWYG module
categories: [drupal, php]
tags: [ckeditor, configuration, css, drupal, module, php, wysiwyg]
---

For a better user experience on node crud actions, I've installed the [WYSIWYG module](http://drupal.org/project/wysiwyg) with the [CKEditor plugin](http://ckeditor.com/). It adds the CKEditor interface as a filter for large textfields. And by default it loads in CKEditor's predefined styles. Very nice for beginners, but what if you want to include your own theme styles? The WYSIWYG editor supports loading your stylesheets, but works a bit buggy on the latest version (2.1 on time of writing this) in combination with the CKEditor. Luckily I found a very quick fix for this issue.
<!-- more -->

After a bit of googling on the issue. I found out that CKEditor v3.6 needs to load [the stylesheetparser plugin](http://docs.cksource.com/CKEditor_3.x/Developers_Guide/Styles#Stylesheet_Parser_Plugin) and this wasn't the case in the WYSIWYG module. With a little help of the smallest module I've had written, your stylesheet's styles replace the default style of the CKEditor.
~~~
<?php
function ckeditor_styles_wysiwyg_editor_settings_alter(&$settings, &$context)
{
    if ($context['profile']->editor == 'ckeditor') {
        $settings['extraPlugins'] .= ',stylesheetparser';

        // Not necessary but removes all default styles from the CKEditor.
        $settings['stylesSet'] = array();
    }
}
~~~

No when you go to *admin/config/content/wysiwyg/profile/filtered_html/edit* and select the option *Define CSS* under *Editor CSS*. You can fill in the stylesheet's uri below. Now you can see your stylesheet's styles on every textarea that uses *filtered_html*.