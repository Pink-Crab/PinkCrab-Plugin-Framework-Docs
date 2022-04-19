# Registerables

<!-- pagenav[start] -->
*Registerable Pages*
* [Registerables](index.md)
* [Post Type](Post_Type.md)
* [Taxonomy](Taxonomy.md)
* [Meta Data](Meta_Data.md)
* [Meta Box](Meta_Box.md)

***
<!-- pagenav[end] -->
<!-- content[start] -->


![Current Version 0.8.0](https://img.shields.io/badge/Current_Version-0.8.0-yellow.svg?style=flat " ") 
[![Open Source Love](https://badges.frapsoft.com/os/mit/mit.svg?v=102)](https://github.com/ellerbrock/open-source-badge/)
[![GitHub_CI](https://github.com/Pink-Crab/Perique-Registerables/actions/workflows/php.yaml/badge.svg)](https://github.com/Pink-Crab/Perique-Registerables/actions/workflows/php.yaml)
[![codecov](https://codecov.io/gh/Pink-Crab/Perique-Registerables/branch/master/graph/badge.svg?token=R3SB4WDL8Z)](https://codecov.io/gh/Pink-Crab/Perique-Registerables)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/Pink-Crab/Perique-Registerables/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/Pink-Crab/Perique-Registerables/?branch=master)

A collection of Abstract Classes for creating common WordPress fixtures which need registering.

## Why? ##

WordPress has a number of Registerable functions for Post Types, Post Meta and Taxonomies. These tend to require large arrays of arguments to be defined. This library provides Classes which can be registered and used with the Registration process.

## Setup ##

```bash 
$ composer require pinkcrab/registerables
``` 

You need to include the module and the Registerable_Middleware. They come with their own dependencies which will need to be added using the construct_registration_middleware() from the App_Factory instance.
```php
$app = ( new PinkCrab\Perique\Application\App_Factory() )
  // Normal Perique bootstrapping.   
  ->construct_registration_middleware( Registerable_Middleware::class );
  ->boot();
```

Once the middleware has been included, you can use Post_Type, Taxonomies, Meta Data and Meta boxes as part of the usual Registration process

## Post Type

Creates a simple post type.

``` php
use PinkCrab\Registerables\Post_Type;

class Basic_CPT extends Post_Type {

  public $key      = 'basic_cpt';
  public $singular = 'Basic';
  public $plural   = 'Basics';
}
```
 
[See full Post Type docs](Post-Type)

## Taxonomy

Creates a flat taxonomy for the **Post** Post Type.

``` php
use PinkCrab\Registerables\Taxonomy;

class Basic_Tag_Taxonomy extends Taxonomy {
  public $slug         = 'basic_tag_tax';
  public $singular     = 'Basic Tag Taxonomy';
  public $plural       = 'Basic Tag Taxonomies';
  public $description  = 'The Basic Tag Taxonomy.';
  public $hierarchical = false;
  public $object_type = array( 'post' );
}
```

[See full Taxonomy Docs](Taxonomy)

## Meta Box

Create a simple meta box as part of a post type definition.
```php
class My_CPT extends Post_Type {
  public $key      = 'my_cpt';
  public $singular = 'CPT Post';
  public $plural   = 'CPT Posts';

  public function meta_boxes( array $meta_boxes ): array {
    $meta_boxes = MetaBox::side('my_meta_box')
      ->label('My Meta Box')
      ->view_template($template_path)
      ->view_vars($additional_view_data)
      ->action('save_post', [$this, 'save_method'])
      ->action('update_post', [$this, 'save_method'])
  }
}
```

> **If your meta box has any level of complexity, it is recommended to create a separate service which handles this and inject it into the Post_Type class.**

```php
/** The Meta Box Service */
class Meta_Box_Service {
  public function get_meta_boxes(): array {
    $meta_boxes = array();
    $meta_boxes[] = MetaBox::side('my_meta_box')
      ->label('My Meta Box')
      ->view_template($template_path)
      ->view_vars($additional_view_data)
      ->action('save_post', [$this, 'save_method'])
      ->action('update_post', [$this, 'save_method'])
  }

  public function save_method( int $post_id ): array {
    // Handle validating and updating post meta.
  }
}

/** Injected into post type */
class My_CPT extends Post_Type {
  public $key      = 'my_cpt';
  public $singular = 'CPT Post';
  public $plural   = 'CPT Posts';

  // Pass the service in as a dependency.
  private Meta_Box_Service $meta_box_service;
  
  public function __construct(Meta_Box_Service $meta_box_service){
    $this->meta_box_service = $meta_box_service;
  }

  // Return the populated Meta_Box instances.
  public function meta_boxes( array $meta_boxes ): array {
    return $this->meta_box_service->get_meta_boxes();
  }
}
```

[See full Meta Box Docs](Meta_Box)

### Shared Meta Boxes

In case you would like to render the same meta box on multiple Custom Post Types or to add it to existing ones, you can use the `Shared_Meta_Box_Controller` base class and extend it to register independent meta boxes.


```php
class Acme_Meta_Box extends Shared_Meta_Box_Controller {
  /**
   * Return the Meta Box instance.
   */
  public function meta_box(): Meta_Box {
    return Meta_Box::side('acme_box')
      ->label('Acme Meta Box')
      ->screen('acme_post_type_a')
      ->screen('acme_post_type_b')
      ->view_template($template_path)
      ->view_vars($additional_view_data)
      ->action('save_post', [$this, 'save_method'])
      ->action('update_post', [$this, 'save_method'])
  }

  /**
   * Sets any metadata against the meta box.
   * @see Post Type docs for more details
   */
  public function meta_data( array $meta_data ): array {
    $meta_data[] = ( new Meta_Data( 'acme_meta_1' ) )
      ->type( 'integer' )
        ->single......
 
    return $meta_data;
  }

  /** The save_post and update_post hook callback */
  public function save_method( int $post_id ): array {
    // Handle validating and updating post meta.
    update_post_meta($post_id, 'acme_meta_1', $value);
  }
}
```
**[Defining Meta Data](Post-Type#registering-meta_data)**

> The above Meta Box would be shown on both `acme_post_type_a` and `acme_post_type_b`  
> You can also inject any dependencies via the constructor too.

## MetaData

You can register `post`, `term`, `user` and `comment` meta fields either as a part of Post Types/Taxonomy Registerables or on there own. This fluent object based definition makes it easy to create these inline.

You can add full REST support by supplying a schema for the field and the Registrar will register the field also.

```php
class Additional_Post_Meta extends Additional_Meta_Data_Controller {
  /** Define the Meta Data */
  public function meta_data(array $meta_data): array {
    $meta_data[] = (new Meta_Data('meta_key'))
      ->post_type('post')
      ->default('foo')
      ->description($description)
      ->single()
      ->sanitize('sanitize_text_field')
      ->rest_schema(['type' => 'string']);

    return $meta_data;
  }
}
```

[See full Meta Data Docs](Meta_Data)  

You can also define MetaData for [Post Types](Post-Type#registering-meta_data) and [Taxonomies](Taxonomy#registering-meta_data) when creating them.

### Additional_Meta_Data_Controller

To register standalone Meta_Data, you can use the `Additional_Meta_Data_Controller` which has a single method `meta_data(array $meta_data): array`. Like in the [example above](#meta-data), you add your Meta_Data instances to the array and return.

The class has an empty constructor, so you can easily inject dependencies in and make use of the `App_Config` meta options.
<!-- content[end] -->
