---
description: >-
  Give iterable access to your collection, allowing for use in while and foreach loops.
---

## Collection Class Documentation
*Pages*
* [Collection](index.md)
* [Typed Collections](Typed_Collections.md)
* [Traits](Traits.md)
    * [Sequence](Trait_sequence.md)
    * [Indexed](Trait_indexed.md)
    * [Is_Iterable](Trait_is_iterable.md)
    * [JSONSeriliable](Trait_jsonserializable.md)
    * [Has_Array_Access](Trait_has_arrayaccess.md)

***

# Is_Iterable

### Custom Collections

To use the collection as a native php iterator, the Is_Iterable trait can be used to implement the Iterable interface.

```php
use Iterator;
use PinkCrab\Collection\Collection;
use PinkCrab\Collection\Traits\Is_Iterable;

class Iterable_Collection extends Collection implements Iterator {
	use Is_Iterable;
}
```

Once a collection is populated with data, it can be used in both foreach and while loops. 

```php
$collection = new Iterable_Collection([1,2,3,4,5,6,7,8,9,10]);

foreach($collection as $num){
    print $num;
}

// 12345678910
```
### Methods

The following methods are required to satisify the Iterator interface.

### rewind\() 
> @return mixed∣null

Resets the internal array pointer to the start of the internal array and returns the first value.

### current\()
> @return mixed∣null  

Returns the value at the current pointer location.

### key\()
> @return string∣int  

Returns the key at the current pointer location.

### next\()
> @return mixed∣false 

Advances the pointer to the next element and returns the value, or false if no next element.

### valid\()
> @return bool

Does the current pointer position have an element