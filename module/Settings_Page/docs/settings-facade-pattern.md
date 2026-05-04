# Settings Façade — a "god class" for settings access

## The problem

Once a plugin grows past a handful of settings, consumers end up writing the same boilerplate in every service:

```php
$value = $this->settings->get( 'some_key', 'fallback' );
$option_name = $this->app_config->post_meta( 'some_meta' );
$post_type = $this->app_config->post_types( 'events' );
$rest_ns = $this->app_config->namespace( 'rest' );
```

Scattered across the codebase this becomes:

- Easy to typo a key (`'some_kye'` fails silently with the fallback)
- Hard to refactor a key name — you have to grep and fix every call site
- Hard to trace which settings/config a service actually depends on
- Mixes two concerns (stored settings vs static config) every time

## The pattern

Introduce a single **Settings Façade** class that:

1. Is **shared** in DI (one instance per request)
2. **Injects** both the `Abstract_Settings` subclass AND `App_Config`
3. Exposes **one named getter per logical setting/key** — callers never see raw string keys
4. Returns **typed values** — string, int, array, whatever the consumer expects
5. Centralises **default values and fallbacks** in one place

Call sites become:

```php
// Before
$limit = $this->settings->get( 'posts_per_page', 10 );

// After
$limit = $this->settings_facade->posts_per_page();
```

Refactoring a key name is now a one-line change in the façade. Callers don't know or care that the key was renamed.

## Example

The façade is **immutable** — constructor-only injection, readonly properties, no setters. Once built, its dependencies can't be swapped. That keeps it safe to share and cache.

```php
<?php

declare( strict_types=1 );

namespace My_Plugin\Settings;

use PinkCrab\Perique\Application\App_Config;
use My_Plugin\Settings\My_Settings; // extends Abstract_Settings

final class Settings_Facade {

    public function __construct(
        private readonly My_Settings $settings,
        private readonly App_Config $app_config,
    ) {}

    /* ─── Stored settings ─── */

    public function posts_per_page(): int {
        return (int) $this->settings->get( 'posts_per_page', 10 );
    }

    public function primary_colour(): string {
        return (string) $this->settings->get( 'primary_colour', '#000000' );
    }

    public function enabled_features(): array {
        $value = $this->settings->get( 'features', array() );
        return is_array( $value ) ? $value : array();
    }

    public function feature_enabled( string $feature ): bool {
        return in_array( $feature, $this->enabled_features(), true );
    }

    public function featured_post_id(): ?int {
        $value = $this->settings->get( 'featured_post' );
        return is_numeric( $value ) ? (int) $value : null;
    }

    public function address_city(): string {
        $address = $this->settings->get( 'address', array() );
        return is_array( $address ) ? (string) ( $address['city'] ?? '' ) : '';
    }

    /* ─── Config-derived keys ─── */

    public function events_post_type(): string {
        return $this->app_config->post_types( 'events' );
    }

    public function featured_meta_key(): string {
        return $this->app_config->post_meta( 'is_featured' );
    }

    public function orders_table(): string {
        return $this->app_config->db_tables( 'orders' );
    }

    public function rest_namespace(): string {
        return $this->app_config->namespace( 'rest' ) ?? 'my-plugin/v1';
    }

    /* ─── Composite / derived values ─── */

    public function is_sidebar_visible_for_post_type( string $post_type ): bool {
        return (bool) $this->settings->get( 'show_sidebar', false )
            && $this->feature_enabled( 'sidebar' )
            && $post_type === $this->events_post_type();
    }
}
```

## DI — nothing to register

Perique's DI autowires by reflection. Since the façade has concrete type-hinted dependencies (`My_Settings`, `App_Config`) and no interfaces, you don't need to add anything to `config/dependencies.php` — just type-hint it anywhere and the container builds it automatically.

If you want it **shared** (same instance reused across services), you can add:

```php
Settings_Facade::class => [ 'shared' => true ],
```

…but that's optional. Autowiring alone works out of the box.

## Wrapping multiple settings classes

You're not limited to one `Abstract_Settings` subclass. A single façade can wrap several — one per settings page, or split by domain:

```php
final class Settings_Facade {

    public function __construct(
        private readonly Display_Settings $display,
        private readonly Integration_Settings $integrations,
        private readonly Email_Settings $emails,
        private readonly App_Config $app_config,
    ) {}

    public function primary_colour(): string {
        return (string) $this->display->get( 'primary_colour', '#000000' );
    }

    public function api_key(): string {
        return (string) $this->integrations->get( 'api_key', '' );
    }

    public function from_email(): string {
        return (string) $this->emails->get( 'from_email', get_option( 'admin_email' ) );
    }

    public function events_post_type(): string {
        return $this->app_config->post_types( 'events' );
    }
}
```

Each inner settings class defines its own fields, persists to its own option, and has its own settings page. The façade is the single read-oriented seam on top — callers never need to know which underlying settings class holds which key.

## Consuming it

Any service, controller, or view just type-hints the façade:

```php
class Event_Archive_Controller {

    public function __construct(
        private Settings_Facade $settings,
    ) {}

    public function render(): void {
        $args = array(
            'post_type'      => $this->settings->events_post_type(),
            'posts_per_page' => $this->settings->posts_per_page(),
            'meta_query'     => array(
                array(
                    'key'   => $this->settings->featured_meta_key(),
                    'value' => '1',
                ),
            ),
        );
        // ...
    }
}
```

No more raw strings for option keys, meta keys, post types, or table names. All the knowledge lives in one place.

## Benefits

| Concern | Before | After |
|---|---|---|
| Key typos | Fail silently via fallback | Impossible — method name catches it at parse time |
| Refactor a key | Grep every call site | Change one method body |
| Type safety | Mixed/string everywhere | Typed return per method |
| Test mocking | Mock `Abstract_Settings` AND `App_Config` | Mock one façade |
| Discovering settings | Read every service | Read one class |
| Default values | Duplicated at every call site | Centralised per method |

## Trade-offs

- **Boilerplate** — one method per setting. Worth it past ~10 keys.
- **Class size** — can grow large. Split by domain if it gets over ~30 methods (e.g. `Display_Settings_Facade`, `Integration_Settings_Facade`).
- **DI coupling** — services depend on the façade rather than the settings model directly. That's usually the point — it's a seam for testing and refactoring.

## When NOT to use it

- Plugins with fewer than ~5–10 settings — overkill.
- One-off scripts or migration code — just read the option directly.
- Where you genuinely need the raw `Abstract_Settings` for iteration (admin pages, export tools).

## Recommended structure

```
src/
├── Settings/
│   ├── My_Settings.php           # extends Abstract_Settings — defines fields
│   └── Settings_Facade.php       # the façade
├── Page/
│   └── My_Settings_Page.php      # extends Settings_Page — uses My_Settings
└── ...
```

The settings class remains the source of truth for field definitions and persistence. The façade is a thin read-oriented layer on top, keeping your domain code clean of raw string keys.
