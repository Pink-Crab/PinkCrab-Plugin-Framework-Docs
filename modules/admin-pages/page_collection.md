---
description: A Collection of all child pages for a defined group.
---

# Page\_Collection

At its heart, the **Page\_Collection** uses the PinkCrab _Collection_  but does not extend it. For the most the collection is used an internal tool and your only interactions are when registering child pages.

## Methods

### public function is\_empty\(\): bool

> @return bool

Checks if any pages have already been added.

### public function add\(Page $page\): self

> @param PinkCrab\Admin\_Pages\Page $page  
> @return self

Allows the adding of a page to the collection. All pages added which do not share the same key as the group,  will be treated as child pages.

```php
$page = new Page(....);
$collection->add($page);

// Is chainable
$page2 = new Page(....);
$collection->add($page)->add($page2);
```

{% hint style="info" %}
This is how pages are added within the set\_child\_pages\(\) method of the Group.
{% endhint %}

## Factories

We have a couple of factory methods on this collection, these can be used to create pages that are already bound to the group. Without the need to pass the group key.

### public function add_child_page( Closure $create ): self

> @param Closure(Page_Factory): Page $create  
> @return self

Alows the passing of a closure for registering a child page. 

```php
$collection->add_child_page(function(Page_Factory $factory): Page {
	return $factory->child_page('child_key2', 'Menu Title')
		->title('Child Page 2 Page Title')
		->view_template('group/child2')
		->view_data(['key' => 'value pairs', 'key2' => true])
        ->position(2)
        ->capability('manage_options');
	}
);

// Nicer in php7.4 syntax
$collection->add_child_page(fn( Page_Factory $factory): Page => $factory
    ->child_page('child_key2', 'Menu Title')
	->title('Child Page 2 Page Title')
	->view_template('group/child2')
	->view_data(['key' => 'value pairs', 'key2' => true])
    ->position(2)
    ->capability('manage_options')
);
```
			