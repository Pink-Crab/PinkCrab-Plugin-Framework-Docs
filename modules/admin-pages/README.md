---
description: Registerable based module for creating Admin Pages and Groups.
---

# Admin Pages

The PinkCrab framework package for creating admin pages/groups using inheritance very easily from plugins and themes.

### Why?

Creating many of WordPress's internal fixtures can sometimes be very verbose with large arrays of values which do not throw errors if incorrect.

The PinkCrab Registerables module provides a small selection of Abstract Classes which can be extended and added to the registration system.

### Dependencies

* Requires the PinkCrab Framework V0.3 and above.

### Installation

```bash
$ composer require pinkcrab/admin-pages
```

### Examples

```php
<?php
class Single_Menu_Page extends Menu_Page_Group {

    // Define menu details
    public $key        = 'simple_single_page';
    public $menu_title = 'Single Page';
    public $icon_url   = 'dashicons-image-filter';

    // Define page details
    public function set_parent_page( Page $page ): Page {
        return $page
            ->title( 'Single Page Title' )
            ->view_template( 'admin/page/page-single' )
            ->view_data( $this->page_contents() );
    }

    // Returns the data for the view.
    protected function page_contents(): array{
        return ['whatever' => 'is needed for your template'];
}
```

```php
use PinkCrab\Admin_Pages\Page;
use PinkCrab\Core\Application\App;
use My_Plugin\Something\My_Service;
use PinkCrab\Admin_Pages\Page_Validator;
use PinkCrab\Core\Interfaces\Renderable;
use My_Plugin\Something\My_Other_Service;
use PinkCrab\Admin_Pages\Menu_Page_Group;
use PinkCrab\Admin_Pages\Page_Collection;

class My_Admin_Group extends Menu_Page_Group {

    public $key        = 'my_admin_group';
    public $menu_title = 'Admin Group';

	/**
	 * Creats an instance of a Menu_Page_Group injected with our
	 * needed additional dependencies.
	 *
	 * The intial $app must be passed to the parent constructor.
	 *
	 * @param \My_Plugin\Something\My_Service $my_service
	 * @param \My_Plugin\Something\My_Other_Service $my_other_service
	 * @param \PinkCrab\Core\Application\App $app
	 */
	public function __construct(
		My_Service $my_service,
		My_Other_Service $my_other_service,
		App $app
	) {
		// Ensure parent constructor is populated and ran as expected!
		parent::__construct( $app );

		$this->my_service       = $my_service;
		$this->my_other_service = $my_other_service;
	}

    /**
     * Register the parent/main page.
     *
     * @param Page $page
     * @return Page $page
     */
    public function set_parent_page( Page $page ): Page {
        return $page
            ->title( 'Inital Page for the group title' )
            ->view_template( 'admin/page/page-one' )
            ->view_data( $this->my_other_service->page_one_data() );
    }

    /**
	 * Register all child pages.
	 *
	 * @param Page_Collection $children
	 * @return Page_Collection
	 */
	public function set_child_pages( Page_Collection $children ): Page_Collection {

		// Populate from a seperate method, returns the populated page.
		$children->add( $this->child_page_one() );

		// Using the factory method in the Child Page Collection, 
		$children->add_child_page(
			function( Page_Factory $factory ): page {
				return $factory->child_page( 'Page Two', 'page_2' )
					->title( 'Page Title for Page Two' )
					->view_template( 'admin/page/page-two' )
					->view_data( $this->my_other_service->page_two_data() );
			}
		);


		return $children;
	}

	/**
	 * Holds the configuration for our child page.
	 *
	 * @return Page
	 */
	public function child_page_one(): Page {
		
		// Create the page using the static constructor for a Page
		// Arguments = age key/slug, menu title & parent key/slug
		$page = Page::create_page( 'page_one', 'Page One', $this->key );		

		$page->title( 'Page Title for Page One' );
		$page->position( 3 ); // Show last

		// Set the view details
		$page->view_template( 'admin/page/page-one' );
		$page->view_data(
			array(
				'header'   => $this->my_service->pages->header,
				'sections' => $this->my_service->pages->get_sections( 'page_one' ),
				'footer'   => $this->my_service->pages->footer,
				'user'     => \get_current_user(),
			)
		);

		return $page;
	}
}
```

### Renderable

All the page templates make use of whichever Renderable implementation is currently setup.

