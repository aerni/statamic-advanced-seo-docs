---
description: >-
  Got some custom routes and don't want to reinvent the SEO implementation? No
  worries, Advanced SEO got you covered.
---

# Custom Routes

## Enable Processing

To enable Advanced SEO for a custom route, pass `seo_enabled` with a value of `true` to the view:

```php
// Works with Statamic routes …
Route::statamic('/login', 'layout', [
    'seo_enabled' => true
]);

// … and works with Laravel routes
Route::get('/login', fn () => view('layout', [
    'seo_enabled' => true
]));
```

Simply adding this value will enable Advanced SEO to process global fields like site name, social images, and noindex.&#x20;

## Adding Contextual Data

Advanced SEO will process any value whose key corresponds to one of its blueprint fields. The most basic data you should add is a meta title and description:

```php
Route::statamic('/login', 'layout', [
    'seo_enabled' => true,
    'seo_title' => 'Login',
    'seo_description' => 'Welcome back! Login to your account.'
]));
```

You can also override any global default value like site name and title separator:

```php
Route::statamic('/login', 'layout', [
    'seo_enabled' => true,
    'site_name' => 'Statamic',
    'title_separator' => '–'
]));

```

{% hint style="info" %}
Each value is processed and augmented by the blueprint field corresponding to its key. This means that a value needs to be in the format expected by its field. For example, you can't add an array to the `seo_title` field as the blueprint field expects a string.
{% endhint %}
