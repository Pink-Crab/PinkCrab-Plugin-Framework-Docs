---
description: >-
  An extension of the Page Model, to allow the creation of ACF Options Pages
  within a Menu_Group
---

# ACF\_Page

All the following properties and methods are extended from the Page model.

{% hint style="info" %}
ACF Pages do not render a template, so passing or setting values to view\_tempalte or view\_data will not be reflected on your page.
{% endhint %}

## Properties

### public $post\_id

> @var string  
> @default = 'options'

Denotes which page should be selected to save and load values from.

### public $autoload

> @var bool  
> @default = false

Should all defined options be autoloaded

### public $updated\_message

> @var string  
> @default = '*Options Updated*'

The message displaye when ACF updates its options.


### public $update\_button

> @var string  
> @default = '*Update*'

The text value of the update options button.

## Methods

### public updated\_message\( string $updated\_message \): self

> @param string $updated\_message  The message shown when a page is updated
> 
> @return self

Set the message shown when a page is updated

### public function update\_button( string $update\_button ): self

> @param string $update_button  The update button text
> 
> @return self

Set the update button text

