---
description: >-
  The core components of the PinkCrab framework, a small and highly extendable
  framework for building WordPress Plugins and Themes.
---

# PinkCrab Framework::Core V0.4.0

## Why?

WordPress is powerful tool for building a wide range of website, but due to its age and commitment to backwards compatibility. Its often fustrating to work with using more modern tools.

The PinkCrab Framework allows the creation of Plugins, Themes and MU Libraries for use on more complex websites.

The Core only provides access to the Loader, Registration, Collection, DI (DICE Dependency Injection Container), App_Config and basic (native) PHP render engine for view.

## Extendable

We have a varied range of additional modules and dependencies. With use of these, you can easily work with Public and Admin api's in a more object oriented fashion. Extensions provide wrappers around creating Post Types, PSR Complient (transient and file) caching, Blade templating, WPDB Migrations, Admin and Settings page creation.

On top of our collection of Extensions various hooks are fired during the intialisation of the plugins App boot process. This allows for hooking additional plugins to a single core App, further seperating conserns.

## Dependency Injection, Registration & Hook Loader

As most code you are likely to write for WordPress is based off Action and Filter calls. The Plugin Framework is built around a Registration process which used to define hook subscriptions and trigger many of WordPress's registerable features (Post Types, Menu Pages, Enqueued Scripts and Styles).

Classes can be stacked up and all processed at initalisation. Through the use of both Dependency Injection and extendable middleware which each class to passed though. You are able to create various Controllers, Services, Factories with DI.

```php
<?php

class Post_Controller implements Registerable {

    protected $post_repository;
    protected $some_service;

    public __cosntruct(Post_Repository $post_repository, Some_Service $some_service){...}

    public function register(Loader $loader): void {
        $loader->front_filter('the_title', [$this, 'modify_title'], 10, 2);
        $loader->front_action('some_action', [$this, 'do_something']);
    }

    public function modify_title(string $title, int $post_id): string {
        if($this->post_repository->can_modify_title($post_id)){
            $title = $this->some_service->something_post_title($post_id);
        }
        return $title;
    }

    public function do_something(WP_Post $post): void {
        if( $this->post_repository->can_do_something($post->ID) ){
            $this->some_service->render_something($post);
        }
    }
}
```
> Makes use of the Renderable interface & middleware. This ensures ```register()``` is passed the current **Hook Loader**

## Simple Setup

While this is primarily made for creating plugins, you can easily use this for themes and MU functionality. All you need is access to composer and to add the core package. 

```bash
$ composer require pinkcrab/plugin-framework 
```

Once you have added all of you additional modules, you can bootstrap the application. This can be done on ```plugin.php``` or your themese ```functions.php```. Three additional files are required, these should return an array containing the setup details.

```php 
require_once __DIR__ '/vendor/autoload.php';

( new App_Factory() )->with_wp_dice( true )
	->di_rules( require __DIR__ . '/config/dependencies.php' )
	->app_config( require __DIR__ . '/config/settings.php' )
	->registration_classes( require __DIR__ . '/config/registration.php' )
	->boot();
```
> For a detailed setup guide please [see here](setup.md)
