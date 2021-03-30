---
description: >-
  Page Models hold all the information required for the page to registers and
  render its view.
---

# Page

## Properties

Each page is comprised of a number of public properties, which can either be defined as $page-&gt;property or using the defined setters.

### public $key

> @var string  
> @required

The pages key/slug

### public $menu\_title

> @var string  
> @required

The menu\_title is shown in the submenu of the defined group.

### public $parent\_slug

> @var string  
> @required

The parent slug denotes which page \(if any\) this pages is a child of. 

{% hint style="info" %}
If this is being set a parent page, set the parent\_slug to '' \(blank string\)
{% endhint %}

### public $position

> @var int  
> @default = 1

Order of the page within the submenu

### public $title

> @var string

The pages title

### public $view\_template

> @var string  
> @default = ""

The view template to be rendered for the page. The Rendering engine will depend on the one set in your **config/dependencies.php** file.

### public $view\_data

> @var array  
> @default = \[ \]

The data passed to the view when it is rendered. All keys must be compatible with PHP variables \(alphanumeric or underscores only\).

```php
// Creating a page with only properties
$page = new Page('my_slug');
$page->menu_title = 'Meny Title';
$page->parent_slug = 'my_group');
$page->position = 2;
$page->title = 'My Page Title.';
$page->view_template = 'pages/my_page';
$page->view_data = ['key' => 'value'];
```

## Methods

To create your pages, there are a few methods and helper methods you can use.

### public function title\( string $title \): Page

> @param string $title  
> @return self

Used to set the title**.**

```php
$page = new Page(...);
$page->title('Page Title');
```

### public function position\( int $position \): Page

> @param int $position  
> @return self

Used to set the position

```php
$page = new Page(...);
$page->position(9);
```

### public function view\_template\( string $view\_template \): Page

> @param string $view\_template  
> @return self

Used to set the view\_template

```php
$page = new Page(...);
$page->view_template('some_path');
```

### public function view\_data\( array&lt;string, mixed&gt; $view\_data \): Page

> @param array&lt;string, mixed&gt; $view\_data  
> @return self

Used to set the view\_template

```php
$page = new Page(...);
$page->view_data(['key' => 'value']);
```

### public static function create\_page\( string $key, string $menu\_title, ?string $parent\_slug = null \): Page

> @param string $key The pages slug   
> @param string $menu\_title The pages menu title  
> @param string\|null $parent\_slug The parent slug, leave blank if parent page\)  
> @return self

Creates a page with the key, menu title and parent slug preset.

```php
$page = Page::create_page('my_slug', 'My Page', 'my_group'); 
```

{% hint style="info" %}
All the setters return the current instance, so can be used fluently.
{% endhint %}

```php
// Create page using fluent setters.
$page = Page::create_page('my_slug',  'Meny Title', 'my_group');
  ->position(2)
  ->title('My Page Title.')
  ->view_template('pages/my_page')
  ->view_data(['key' => 'value']);
```

