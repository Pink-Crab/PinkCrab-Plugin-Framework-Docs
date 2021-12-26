# View

The Renderable interface can be injected into any class which you plan to construct using the Apps container. Combined with the Registration system, this allows for the creation of Controllers that can render views based on hook calls.

```php
class Post_Controller implements Registerable {
	/**
	 * @param View $view
	 */
	 protected $view;

	public function __construct( View $view ) {
		$this->view = $view;
	}

	/**
	 * @param Loader $loader
	 */
	public function register( Loader $loader ): void {
		$loader->filter( 'the_content', [ $this, 'render_content' ], 1, 20 );
	}

	/**
	 * @param string $content Inital content
	 * @return string Replaced with custom view template, passing in the post
	 */
	public function render_content( string $content ): string {
		return is_single() && get_post_type() === 'post'
			? $this->view->render( 
				'post/single', // for path "views/post/single.php"
				[ 'post' => get_post() ], 
				View::RETURN_VIEW // false, true(default) for print  
			)
			: $content;
	}
}
```

## Data

All data you wish to have access to can be passed into the `render()` methods as an array. The array keys are then cast into variables, allowing for cleaner HTML/PHP code. The standard PHP variable name rules apply, so all keys should be valid. Arrays and Objects can be passed into the views, allow for partials to be rendered too.

```php
$this->view->render('test/view',[
    'number'  => 5, 
    'colours' => ['red','green','blue'],
]);

// file - views/test/view.php

<?php
// Test View
?>
<p>We have a magic number, its $number <?php echo (int) $number; ?></p>
<ul>
<?php foreach($colours as $colour): ?>
    <li><?php echo esc_html( $colour );?></li>
<?php endforeach; ?>
</ul>
```

> **NO SANITIZATION OR ESCAPING IS CARRIED OUT AUTOMATICALLY AND SHOULD BE DONE IN THE TEMPLATE!**


## Partials

You can easily render a partial within your template. You have access to the PHP\_Engine class in your view, so you can call render\(\).

```php
$this->view->render('teams/members',[
    'title'  => 'Team 2', 
    'members' => [
        ['name' => 'Joe Bloggs', 'position' => 'Team Cpt.'.....],
        .......
    ],
]);

// file - views/teams/members.php

<?php
// Members list
?>
<h2><?php echo $title; ?></h2>
<p>Welcome to the team page for <?php echo $title; ?>.</p>
<p>Members</p>
<div>
    <?php foreach($members as $member) {
        $this->render('team/member-profile', $member, View::PRINT_VIEW );
    } ?>
</div>

// file - views/member-profile.php

<?php
// Members profile partial
?>
<div>
    <h2><?php echo $name; ?></h2>
    </p>Role: <span><?php echo $position; ?></span></p>
</div>
```
> If for any reasion you can not access the view instance passed as a dependency, you can call it statically using `App::view()->render('template', ['data'=>true]);`

## Output Buffer

Many times when you are working with WordPress, functionality will directly output. This is fine if you are calling these from within templates, but can be problematic for Ajax calls and composing views programmatically in general.

Rather than calling the ob_*() functions directly, the View class comes with a handy helper (static) method.

```php
/**
 * Buffer for use with WordPress functions that display directly.
 *
 * @param callable $to_buffer
 * @return string
 */
public static function print_buffer( callable $to_buffer ): string
```
> Best to pass an anoymous function, which calls the printing function/snippet
```php
class Something {
	public function create_some_view($data): string{
		return View::print_buffer(function() use ($data){
			// This function will echo/print something.
			i_echo_something($data);
		});
	}
}

// Usage.

function something_with_wrapper($data): string {
	$some_class = new Something();
	return sprintf(
		'<div class="something">%s</div>', 
		$some_class->create_some_view($data)
	);
}
```

## Render Type

Like with the output buffer sometimes you need to generate the HTML string representation of a view, rather than print it to the output. The `View::render()` method allows doing either.