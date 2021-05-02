---
description: >-
  The primary class which is extended to create either groups of pages or single
  top level pages.
---

# Menu\_Page\_Group \(Abstract\)

## Properties

The Admin\_Pages primary group is configured with the following properties, only $key & $menu\_title is required.

### public $key

> @var string  
> @required

The main key/slug used for the group. 

### public $menu\_title

> @var string  
> @required

The `$menu_title` is shown on the main wp-admin menu and on the primary page in the sub-menu.

### public $icon\_url

> @var string  
> @default = 'dashicons-admin-generic'

The Icon to be used for the wp-admin menu. Can be URL or dashicon as per `add_menu_page()`.

### public $capability

> @var string  
> @default = 'manage\_options'

The minimum capability the user must-have for the group to show on the main menu. All child pages follow the same capability requirements

### public $position

> @var int  
> @default = 85

Follows standard WP conventions on Menu positioning. 

## Methods

To create your pages, there are a few methods and helper methods you can use.

### public function set\_parent\_page\( Page $parent\_page \): Page

> @param PinkCrab\Admin\_Pages\Page $parent\_page  
> @return PinkCrab\Admin\_Pages\Page

This method is used to define the parent page for your group. A base **Page Model** is injected into the method, so it just a case of populating and returning. See the Page pages for more details on **Page Model.**

```php
public function set_parent_page( Page $parent_page ): Page {		
		return $parent_page
				->title('My Pages Title')
				->view_tempalte('some/path')
				->view_data(['key' => 'value pairs', 'key2' => true']);
}
```

### public function set\_child\_pages\( Page\_Collection $children \): Page\_Collection

> @param PinkCrab\Admin\_Pages\Page\_Collection $children  
> @return PinkCrab\Admin\_Pages\Page\_Collection

This method is used to register as many child/sub pages to your collection. So long as the child page is populated with the same key as the group, it will all be rendered together.

```php
public function set_child_pages( Page_Collection $children ): Page_Collection {		
		
		// Create the page.
		$child_page = Page::create_page('child_key1', 'Menu Title', 'parent_key' );
		$child_page->title('Child Page 1 Page Title');
		$child_page->view_template('group/child1');
		$child_page->view_data(['key' => 'value pairs', 'key2' => true]);
		
		// Add page to collection.
		$children->add($child_page);
		
		return $children;
}
```

The Page\_Collection comes with a nice little child page factory included. This doesnt need the parent group being defined and thanks to the fluent API, can be written more condensed.

```php
public function set_child_pages( Page_Collection $children ): Page_Collection {		
		$children->add_child_page(
			function( Page_Factory $factory ): Page {
				return $factory->child_page('child_key2', 'Menu Title')
					->title('Child Page 2 Page Title')
					->view_template('group/child2')
					->view_data(['key' => 'value pairs', 'key2' => true]);
			}
		);
		
		return $children;
}
```

{% hint style="info" %}
The Page\_Collection has a few other helper methods, see the docs for more details.  
{% endhint %}

### public function setup\(\): void

> @return void

This method is fired before the Group has been added to the loader. You can use this method to make any final changes.

## Injecting Additional Dependencies

Obviously injecting dependencies into your Group will help you in populating your pages. The constructor currently requires App to be passed as a dependency, so ensure it is passed to the parent construtor if injecting additional dependencies.

```php
	public function __construct(
		My_Service $my_service,
		My_Other_Service $my_other_service,
		App $app
	) {
		// Ensure parent constructor is populated and ran as expected!
		parent::__construct( $app);

		$this->my_service       = $my_service;
		$this->my_other_service = $my_other_service;
	}
```

