---
description: Add SEO support to custom Statamic or Laravel routes without reinventing the SEO implementation.
---

# Custom Routes

{% hint style="info" %}
Custom routes require the [Pro edition](../getting-started/editions.md).
{% endhint %}

## Seo::data()

Use the `Seo::data()` fluent builder to add SEO metadata to custom routes:

```php
use Aerni\AdvancedSeo\Facades\Seo;

Route::statamic('/login', 'login', [
    'title' => 'Login',
    ...Seo::data()
        ->title('Login')
        ->description('Welcome back! Login to your account.')
        ->toArray(),
]);
```

This also works with regular Laravel routes:

```php
Route::get('/login', function () {
    return view('login', [
        'title' => 'Login',
        ...Seo::data()
            ->title('Login')
            ->description('Welcome back! Login to your account.')
            ->toArray(),
    ]);
});
```

### Available Methods

| Method | Description |
| ------ | ----------- |
| `title(string)` | Set the meta title. |
| `description(string)` | Set the meta description. |
| `ogTitle(string)` | Set the Open Graph title. |
| `ogDescription(string)` | Set the Open Graph description. |
| `ogImage(string\|Asset)` | Set the Open Graph image. Accepts a URL or Statamic Asset. |
| `noindex()` | Mark the page as noindex. |
| `nofollow()` | Mark the page as nofollow. |
| `canonicalUrl(string)` | Set a custom canonical URL. |
| `jsonLd(string\|Type)` | Set JSON-LD structured data. Accepts a raw JSON string or a `Spatie\SchemaOrg\Type` instance. |
