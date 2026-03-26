---
description: Add SEO support to custom Statamic or Laravel routes without reinventing the SEO implementation.
---

# Custom Routes

You can enable Advanced SEO for custom routes by passing `seo_enabled` along with your SEO data to the view. The data keys correspond to the SEO field handles.

```php
// Works with Statamic routes …
Route::statamic('/login', 'layout', [
    'seo_enabled' => true,
    'seo_title' => 'Login',
    'seo_description' => 'Welcome back! Login to your account.',
]);

// … and with Laravel routes
Route::get('/login', fn () => view('layout', [
    'seo_enabled' => true,
    'seo_title' => 'Login',
    'seo_description' => 'Welcome back! Login to your account.',
]));
```

You can also override site defaults like site name and separator:

```php
Route::statamic('/login', 'layout', [
    'seo_enabled' => true,
    'site_name' => 'Statamic',
    'separator' => '–',
]);
```

{% hint style="info" %}
Each value is processed and augmented by the blueprint field corresponding to its key. This means that a value needs to be in the format expected by its field. For example, you can't add an array to the `seo_title` field as it expects a string.
{% endhint %}
