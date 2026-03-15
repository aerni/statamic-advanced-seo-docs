---
description: >-
  Register custom sitemaps to add any Statamic or Laravel route to
  Advanced SEO's sitemap index.
---

# Custom Sitemaps

Custom sitemaps let you add any route to Advanced SEO's sitemaps. Register them in a service provider's `boot` method.

## Closure-Based Registration

Use the `Sitemap` facade to build and register a custom sitemap:

```php
use Aerni\AdvancedSeo\Facades\Sitemap;

public function boot(): void
{
    Sitemap::index('default')->add(function () {
        $signIn = Sitemap::makeUrl('https://example.com/sign-in');

        $pricing = Sitemap::makeUrl('https://example.com/pricing')
            ->lastmod(now())
            ->changefreq('weekly')
            ->priority('0.8')
            ->alternates([
                ['href' => 'https://example.com/pricing', 'hreflang' => 'en'],
                ['href' => 'https://example.com/de/pricing', 'hreflang' => 'de'],
            ]);

        return Sitemap::make('marketing')
            ->add($signIn)
            ->add($pricing);
    });
}
```

The first argument to `Sitemap::index()` is the site handle that determines which domain's sitemap index the custom sitemap belongs to.

## Class-Based Registration

For larger sitemaps, move the logic into its own class:

```php
use Aerni\AdvancedSeo\Facades\Sitemap;
use App\Sitemaps\MarketingSitemap;

public function boot(): void
{
    Sitemap::index('default')->add(MarketingSitemap::class);
}
```

The sitemap class extends `BaseSitemap` and implements the `urls()` method:

```php
namespace App\Sitemaps;

use Aerni\AdvancedSeo\Facades\Sitemap;
use Aerni\AdvancedSeo\Sitemaps\BaseSitemap;
use Illuminate\Support\Collection;

class MarketingSitemap extends BaseSitemap
{
    public function type(): string
    {
        return 'custom';
    }

    public function handle(): string
    {
        return 'marketing';
    }

    public function urls(): Collection
    {
        return collect([
            Sitemap::makeUrl('https://example.com/sign-in'),
            Sitemap::makeUrl('https://example.com/pricing')
                ->lastmod(now())
                ->changefreq('weekly')
                ->priority('0.8'),
        ]);
    }
}
```

## API Reference

### Sitemap Facade

| Method | Description |
| --- | --- |
| `Sitemap::index($site)` | Get the sitemap index for a site |
| `Sitemap::make($handle)` | Create a new custom sitemap |
| `Sitemap::makeUrl($loc)` | Create a new sitemap URL |

### CustomSitemapUrl

All setter methods are fluent and return the URL instance:

| Method | Description | Default |
| --- | --- | --- |
| `->lastmod($carbon)` | Last modification date | `now()` |
| `->changefreq($freq)` | Change frequency | From config |
| `->priority($priority)` | Priority (0.0–1.0) | From config |
| `->alternates($array)` | Hreflang alternates | `null` |

Valid `changefreq` values: `always`, `hourly`, `daily`, `weekly`, `monthly`, `yearly`, `never`.
