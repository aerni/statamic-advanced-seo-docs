---
description: Automatically generated XML sitemaps for your collections and taxonomies.
---

# Sitemaps

Advanced SEO generates sitemaps for all of your collections and taxonomies. The sitemaps are organized in a sitemap index, accessible at `/sitemap.xml`.

## Generating Sitemaps

Sitemaps are generated on demand whenever a sitemap is visited on the frontend. For content-heavy sites, you can pre-generate them with the following command:

```shell
php please seo:generate-sitemaps
```

Add the `--queue` flag to generate the sitemaps in the background:

```shell
php please seo:generate-sitemaps --queue
```

{% hint style="info" %}
Make sure that your current environment is [enabled for crawling](configuration.md#crawling). Otherwise, you won't be able to generate the sitemaps.
{% endhint %}

The recommended approach is to [schedule the command](https://laravel.com/docs/master/scheduling#scheduling-artisan-commands):

```php
Schedule::command('seo:generate-sitemaps --queue')->hourly();
```

You may change the storage path for generated sitemaps in the config:

```php
'sitemap' => [
    'path' => storage_path('statamic/sitemaps'),
],
```

## Disable Sitemaps

You may globally disable the sitemap feature in the config:

```php
'sitemap' => [
    'enabled' => false,
],
```

This removes all sitemap-related settings from the Control Panel and disables the sitemap frontend routes.

## Disabling Collections & Taxonomies

You can disable sitemaps for individual collections and taxonomies using the **Sitemap** toggle in the [collection/taxonomy configuration](settings-and-defaults.md#collection--taxonomy-configuration).

Sitemaps are also excluded when **Noindex** has been enabled in the collection/taxonomy defaults.

## Excluding Individual Entries and Terms

An entry or term is excluded from the sitemap when any of the following conditions are met:

* **Sitemap toggle** is disabled on the entry or term.
* **Noindex** is enabled on the entry or term.
* **Canonical URL** points to a different entry or a custom URL.

## Domain Scoping

In multi-site setups with multiple domains, each domain gets its own sitemap index. Sitemaps only contain URLs from sites on that domain.

| Setup                                                  | Sitemap Indexes                                         |
| ------------------------------------------------------ | ------------------------------------------------------- |
| `example.com`, `example.com/de`, `example.com/fr`      | One index on `example.com`                              |
| `example.com`, `example.com/de`, `example.fr`          | One index on `example.com`, one on `example.fr`         |
| `example.com`, `example.de`, `example.fr`              | One index per domain                                    |

{% hint style="info" %}
Hreflang tags are unaffected and continue to reference all localized versions across domains.
{% endhint %}

## Custom Sitemaps

You can add custom routes to Advanced SEO's sitemaps. See [Custom Sitemaps](../extending/sitemaps.md) for details.
