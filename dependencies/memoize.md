---
description: >-
  Adds a simple Memoize based object cache to give a simple interface for
  caching potentially expensive repeat operations within a class
---

# Memoize

## Memoize

Simple trait for adding a Memoize object cache to your class

![ ](https://img.shields.io/badge/Current_Version-1.0.0-green.svg?style=flat) ![ ](https://img.shields.io/badge/PHPStan-level%208-brightgreen.svg?style=flat) ![ ](https://img.shields.io/badge/PHPUnit-PASSING-brightgreen.svg?style=flat)

### Version

**Release 1.0.0**

### Why?

Adds an internal cache with a memoize interface. Creates a cache of results based on a hash of the arguments used for the call.

### Setup

```bash
$ composer require pinkcrab/memoize-trait
```

```php
use PinkCrab\Memoize\Memoizable;

class CategoryRepository {

    /**
     * Gives access to the Memoize cache
     */
    use Memoizable;

    /** 
     * Will call the database on the first call.
     * Creates a cache based on the hash of $id & $parent (concatinated)
     */
    public function getCategory($id, $parent): ?CategoryTerm {
        return $this->memoize(
            $this->generateHash($id, $parent),
            function () use ($id, $parent): ?CategoryTerm {
                return $this->slowDataSource->someExpensiveQuery($id, $parent);
            }
        )
    }
}
```

All the methods are protected and are not intended as part of an objects public Inteface.

## Methods

### memoize\( string $hash, callable $fetch \): mixed

The hash value should be unique and repeatable/pure, based on the parameters. You can use the built in generateHash\(...$scarla\). The callable passed, should carry out your operation, before the result is returned, it is passed to the internal cache.

```php
public function doExpensiveCall($param1, $param2): Result 
{
    return $this->memoize(
        // Generate Hash
        $this->generateHash($param1, $param2),
        // The fetch/call callable.
        function() use ($param1, $param2) : Result {
            return $this->service
                ->expensiveCall($param1, $param2);
        }
    );
}
```

### generateHash\(...$parts\): string

Allows the passing of any number of serializable variables and being returned a repeatable hash. This only uses MD5 under the hood, you can create a custom hash generator

### flushMemoize\(\): void

Clears the internal cache array.

