# Crawling

## Enable Crawling

The crawling config allows you to configure the environments in which you want the sites to be crawled. By default, crawling is only enabled for the production environment.

```php
'crawling' => [
    'environments' => ['production'],
]
```

## Implications

The environments config affects the rendered meta tags and sitemaps, following best practices to avoid mixed signals to search engines.

### Meta Tags

The robots meta tag will be set to `noindex, nofollow` in all environments that aren't listed in the config. Additionally, all crawling-related meta tags like `canonical` and `alternate` are removed as well.

### Sitemaps

The sitemap routes will be disabled in all environments that aren't listed in the config.
