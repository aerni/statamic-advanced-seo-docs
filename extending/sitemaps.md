---
description: Register custom sitemaps to add any Statamic or Laravel route to Advanced SEO's sitemap index.
---

# Sitemaps

Custom sitemaps let you add any route to Advanced SEO's sitemaps. Each custom sitemap declares which site it belongs to, and the sitemap index automatically includes it when generating sitemaps for that site's domain.

## Class-Based Registration

Create a dedicated class that extends `CustomSitemap`. This is the preferred approach as it keeps your service provider clean:

```php
namespace App\Sitemaps;

use Aerni\AdvancedSeo\Sitemaps\Custom\CustomSitemap;
use Illuminate\Support\Collection;

class MarketingSitemap extends CustomSitemap
{
    protected string $handle = 'marketing';

    protected string $site = 'english';

    public function urls(): Collection
    {
        return collect([
            $this->makeUrl('https://example.com/sign-in'),
            $this->makeUrl('https://example.com/pricing')
                ->lastmod(now())
                ->changefreq('weekly')
                ->priority('0.8'),
        ]);
    }
}
```

The `$site` property determines which site's sitemap index the custom sitemap belongs to. If not set, it defaults to the default Statamic site.

Class-based sitemaps can be registered in two ways:

### Via Config

Add the class to the `custom` array in `config/advanced-seo.php`:

```php
'sitemap' => [
    'custom' => [
        App\Sitemaps\MarketingSitemap::class,
    ],
],
```

### Via Service Provider

Register the class in a service provider's `boot` method:

```php
public function boot(): void
{
    \App\Sitemaps\MarketingSitemap::register();
}
```

## Inline Registration

For simpler sitemaps, you can use the `Sitemap` facade to build and register a custom sitemap inline:

```php
use Aerni\AdvancedSeo\Facades\Sitemap;
use Aerni\AdvancedSeo\Sitemaps\Custom\CustomSitemapUrl;

public function boot(): void
{
    Sitemap::make('marketing')
        ->site('english')
        ->add('https://example.com/sign-in')
        ->add('https://example.com/pricing', function (CustomSitemapUrl $url) {
            $url->lastmod(now())
                ->changefreq('weekly')
                ->priority('0.8')
                ->alternates([
                    ['href' => 'https://example.com/pricing', 'hreflang' => 'en'],
                    ['href' => 'https://example.com/de/pricing', 'hreflang' => 'de'],
                ]);
        })
        ->register();
}
```

## API Reference

### SitemapBuilder

All setter methods are fluent and return the builder instance:

| Method | Description |
| --- | --- |
| `->site($site)` | Set the site handle (defaults to the default Statamic site) |
| `->add($url, $callback)` | Add a URL string with an optional callback to configure the `CustomSitemapUrl` |
| `->register()` | Register this sitemap (must be called last) |

### CustomSitemapUrl

All setter methods are fluent and return the URL instance:

| Method | Description | Default | Values |
| --- | --- | --- | --- |
| `->lastmod($carbon)` | Last modification date | `now()` | A `Carbon` instance |
| `->changefreq($freq)` | Change frequency | `daily` | `always`, `hourly`, `daily`, `weekly`, `monthly`, `yearly`, `never` |
| `->priority($priority)` | Priority | `0.5` | `0.0` to `1.0` in `0.1` increments |
| `->alternates($array)` | Hreflang alternates | `null` | Array of `['href' => ..., 'hreflang' => ...]` |
