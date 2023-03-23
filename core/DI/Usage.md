{% include DI-sections.md %}

# Dependency Injection - Usage


Where DI really comes into its own is using `Interfaces`, this allows you to write code where implementations are are not tightly bound. For an example of this, please see the [Message Example](examples/message_example).

When using interfaces as dependencies, a custom rule will have to be defined, as the implementation can be not be inferred through type hints. There are a few ways to define these rules, for a more detailed explanation, see the [Rules page](Rules)

```php
class Example_With_Interface_Dependency{
    public function __construct(Foo_Interface $foo){
        $this->foo = $foo;
    }
}
```