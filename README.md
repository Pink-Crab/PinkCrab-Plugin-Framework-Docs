---
description: >-
  The core components of the PinkCrab framework, a small and highly extendable
  framework for building WordPress Plugins and Themes.
---

# PinkCrab Framework::Core

The PinkCrab Framework 

### Setup.

You can clone our existing **Plugin Boiler Plate** on [github](https://github.com/Pink-Crab/Framework_Plugin_Boilerplate), if however, you wanted to construct the application you can. The framework can work as a standalone plugin, or setup slightly differently to be used a core library in the mu-plugins directory or driving a more MVC orientated theme.

The framework uses composer, normally this is not advised for WordPress plugins/themes. We have a small config for using [php-scoper](https://github.com/humbug/php-scoper/) to move the entire vendor directory to a prefixed namespace. More details can be found at the end of this page.

{% hint style="info" %}
The rest of this tutorial assumes you have composer installed globally.
{% endhint %}

### Structure

```text
+ plugin
    + config
        | dependencies.php
        | registration.php
        | settings.php
    + src
    | bootstrap.php
    | composer.json
    | plugin.php


// OPTIONAL (Comes included in boilerplate repo)
    + views
    + assets
    + wp
        | Activation.php
        | Deactivation.php
        | Uninstalled.php
    + tests
        | bootsrap.php
        | wp_config.php
    | phpcs.xml
    | phpstan.neon.dist
    | phpunit.xml.dist
```

Create your directory and initialise a composer project with `composer init`from the command line. To add the Framework type `composer require pinkcrab/plugin-framework` to add to your plugin. You can add any additional libraries using **packagist** as you would any other PHP project. Then it's just a case of filling the rest of your composer.json file \([see an example](https://github.com/Pink-Crab/Framework_Plugin_Boilerplate/blob/master/composer.json)\)

Once you have all of your composer.json setup, you can run composer install, and then it's a case of hooking your application up to WordPress. 

## 

