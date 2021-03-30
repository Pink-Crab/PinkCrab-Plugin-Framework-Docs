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

### public function create\_child\_page\(string $menu\_title, string $key \): Page

> @param string $menu\_title  
> @param string $key  
> @return PinkCrab\Admin\_Pages\Page

This creates a partially populated Page with its parent key bound to the group its self.

```php
$page = $collection->create_child_page('Sub Page', 'sub_page_1');
$page->title('Set as normal');
$page->view_tempalte('path');
$page->view_data([.....]);

// Or fluently
$page = $collection->create_child_page('Sub Page', 'sub_page_1')
    ->title('Set as normal')
    ->view_tempalte('path')
    ->view_data([.....]);
```



### public function create\_child\_acf\_page\(string $menu\_title, string $key \): Page

> @param string $menu\_title  
> @param string $key  
> @return PinkCrab\Admin\_Pages\Page

This creates a simple ACF options page, as a child in your group. See the ACF page docs for more details.

```php
$page = $collection->create_child_page('Sub Page', 'sub_page_1');
$page->title('Set as normal');

// Or fluently
$page = $collection->create_child_page('Sub Page', 'sub_page_1')
    ->title('Set as normal');
```



