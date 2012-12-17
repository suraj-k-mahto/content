---
title: Configure multiple environments within Wordpress
categories: [php, wordpress]
tags: [configuration, environment, pagodabox, php, wordpress]
---

Most of the time, when you are developing web applications, you make use of multiple environments. For example, a development, staging and production environment. But how do you configure each environment separately? One solution is a multi-site installation with Wordpress, but I didn't see that one fit into my development process.
<!-- more -->
In my case, the Wordpress is hosted on [Pagoda Box](http://pagodabox.com). A very good hosting solution for PHP applications, but you are very limited when it comes to configuring your Wordpress installation (you have to install plugins locally and commit them to the repository). I've followed [this documentation](http://help.pagodabox.com/customer/portal/articles/175468-wordpress) at first to get Wordpress up and running on Pagoda Box. But I wasn't very pleased with their solution of setting up the database connection both locally and on Pagoda Box. So I came up with my own solution.

With my solution, Wordpress checks for an environment variable called 'ENVIRONMENT' and based on that it loads the appropriate configuration file. It defaults on 'dev', so in your development environment it would load the config file *wp-config/wp-config.dev.php*
To make Wordpress detecting your configuration files, you have to modify *wp-config.php* first to this;
~~~
<?php
/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
    define('ABSPATH', dirname(__FILE__) . '/');

/** Load configuration based on the server's environment **/
$env = isset($_SERVER['ENVIRONMENT']) ? $_SERVER['ENVIRONMENT'] : 'dev';
require_once ABSPATH . 'wp-config/wp-config.' . strtolower($env) . '.php';

/** Sets up WordPress vars and included files. */
require_once(ABSPATH . 'wp-settings.php');
~~~

Now you're environment configuration file could look like this (for connecting with a Pagoda Box database instance for example);
~~~
<?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
 * Secret Keys, WordPress Language, and ABSPATH. You can find more information
 * by visiting {@link http://codex.wordpress.org/Editing_wp-config.php Editing
 * wp-config.php} Codex page. You can get the MySQL settings from your web host.
 *
 * This file is used by the wp-config.php creation script during the
 * installation. You don't have to use the web site, you can just copy this file
 * to "wp-config.php" and fill in the values.
 *
 * @package WordPress
 */

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', 'database_name_here');

/** MySQL database username */
define('DB_USER', 'username_here');

/** MySQL database password */
define('DB_PASSWORD', 'password_here');

/** MySQL hostname */
define('DB_HOST', 'localhost');

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');

/**#@+
 * Authentication Unique Keys and Salts.
 *
 * Change these to different unique phrases!
 * You can generate these using the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}
 * You can change these at any point in time to invalidate all existing cookies. This will force all users to have to log in again.
 *
 * @since 2.6.0
 */
define('AUTH_KEY',         'put your unique phrase here');
define('SECURE_AUTH_KEY',  'put your unique phrase here');
define('LOGGED_IN_KEY',    'put your unique phrase here');
define('NONCE_KEY',        'put your unique phrase here');
define('AUTH_SALT',        'put your unique phrase here');
define('SECURE_AUTH_SALT', 'put your unique phrase here');
define('LOGGED_IN_SALT',   'put your unique phrase here');
define('NONCE_SALT',       'put your unique phrase here');

/**#@-*/

/**
 * WordPress Database Table prefix.
 *
 * You can have multiple installations in one database if you give each a unique
 * prefix. Only numbers, letters, and underscores please!
 */
$table_prefix  = 'wp_';

/**
 * WordPress Localized Language, defaults to English.
 *
 * Change this to localize WordPress. A corresponding MO file for the chosen
 * language must be installed to wp-content/languages. For example, install
 * de_DE.mo to wp-content/languages and set WPLANG to 'de_DE' to enable German
 * language support.
 */
define('WPLANG', '');

/**
 * For developers: WordPress debugging mode.
 *
 * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 */
define('WP_DEBUG', false);

/** Sets up WordPress vars and included files. */
require_once(ABSPATH . 'wp-settings.php');
~~~