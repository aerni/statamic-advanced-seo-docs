---
description: Upgrade from Advanced SEO 2.0 to 3.0. Most breaking changes are handled by automated upgrade scripts.
---

# Upgrade 2.0 to 3.0

Advanced SEO 3.0 introduces a significant internal refactor that improves the architecture, maintainability, and user experience of the addon. Most breaking changes have automated upgrade scripts, so the upgrade should be seamless for most users.

## Upgrade Summary

The following changes require manual action. All other changes are [handled automatically](upgrade-2-to-3.md#automated-changes).

| Change                                                                                 | Impact | Action                       |
| -------------------------------------------------------------------------------------- | ------ | ---------------------------- |
| [Screenshot package](upgrade-2-to-3.md#screenshot-package)                             | High   | Require package manually     |
| [Blueprint customization removed](upgrade-2-to-3.md#blueprint-customization-removed)   | High   | Remove custom fields         |
| [GraphQL](upgrade-2-to-3.md#graphql)                                                   | Medium | Update queries               |
| [Custom sitemap registration](upgrade-2-to-3.md#custom-sitemap-registration)           | Medium | Update code                  |
| [Events](upgrade-2-to-3.md#events)                                                     | Medium | Update event listeners       |
| [Custom views (Twitter)](upgrade-2-to-3.md#custom-views)                               | Low    | Update published views       |
| [Unified social image field](upgrade-2-to-3.md#unified-social-image-field)             | Low    | Update published views       |
| [Generator config changes](upgrade-2-to-3.md#generator-config-changes)                 | Low    | Remove from published config |
| [Removed Twitter image generation](upgrade-2-to-3.md#removed-twitter-image-generation) | Low    | Remove custom templates      |
| [Social images template changes](upgrade-2-to-3.md#template-changes)                   | Low    | Update custom templates      |
| [Image storage directory](upgrade-2-to-3.md#image-storage-directory)                   | Low    | Delete orphaned images       |

## High Impact Changes

### Requirements

* PHP 8.4+
* Laravel 12+
* Statamic 6+

### Screenshot Package

Affects users of the social images generator.

The social images generator now uses [spatie/laravel-screenshot](https://spatie.be/docs/laravel-screenshot/v1/introduction) and is no longer bundled as a dependency. This package supports Browsershot and Cloudflare Browser Rendering as screenshot drivers.

To restore existing functionality, require both the screenshot package and Browsershot:

```shell
composer require spatie/laravel-screenshot spatie/browsershot
```

Alternatively, you can use Cloudflare Browser Rendering instead of Browsershot. Refer to the [installation guide](https://spatie.be/docs/laravel-screenshot/v1/installation-setup) for details. To customize screenshot settings, publish the config:

```shell
php artisan vendor:publish --tag=laravel-screenshot-config
```

### Blueprint Customization Removed

Affects users who customized Advanced SEO fieldsets or used the `advanced_seo` fieldtype in their blueprints.

Support for blueprint customization has been removed. This includes:

* The **Advanced SEO fieldsets** (e.g. `Advanced SEO (Main)`, `Title & Description`, `Open Graph`, etc.) that could be edited to customize which fields appear in the SEO tab
* The **Advanced SEO fieldtype** (`advanced_seo`) that allowed placing individual SEO fields anywhere in a blueprint

Remove any `advanced_seo` fieldtype fields from your blueprints and delete any customized Advanced SEO fieldsets. The SEO tab fields are now managed entirely by the addon and cannot be customized.

## Medium Impact Changes

### GraphQL

Affects users of the GraphQL API. All GraphQL changes require updating your queries.

#### Renamed Query and Types

The `seoDefaults` query has been renamed to `seoSet`:

```diff
- seoDefaults {
+ seoSet {
    ...
  }
```

All type names have been updated to use "Set" instead of "Defaults":

| Old Type Name     | New Type Name                      |
| ----------------- | ---------------------------------- |
| `siteDefaults`    | `siteSet`                          |
| `contentDefaults` | `collectionSet` / `taxonomySet`    |

#### Flattened Site Set Structure

The `siteDefaults` type previously contained nested sub-types. These have been consolidated into a single flat `siteSet` type:

```diff
  {
-   seoDefaults {
-     site {
-       general { site_name, ... }
-       indexing { noindex, ... }
-       socialMedia { og_image, ... }
-       analytics { fathom_id, ... }
-       favicons { favicon_svg, ... }
-     }
-   }
+   seoSet {
+     site { site_name, noindex, og_image, fathom_id, favicon_svg, ... }
+   }
  }
```

<details>
<summary>Removed sub-types</summary>

| Removed Type         | Replacement                             |
| -------------------- | --------------------------------------- |
| `generalDefaults`    | Fields moved directly onto `siteSet`    |
| `indexingDefaults`   | Fields moved directly onto `siteSet`    |
| `socialMediaDefaults`| Fields moved directly onto `siteSet`    |
| `analyticsDefaults`  | Fields moved directly onto `siteSet`    |
| `faviconsDefaults`   | Fields moved directly onto `siteSet`    |

</details>

#### Removed `seoSet` Fields

The following fields have been removed from the `siteSet` type as they are now configured per collection/taxonomy:

* `excluded_collections`
* `excluded_taxonomies`
* `social_images_generator_collections`

#### Disabled Features

Fields belonging to disabled features are now completely removed from the schema. Previously, disabled feature fields were still present but returned `null`. Now, if a feature is disabled in `config/advanced-seo.php`, its fields will not appear in the schema at all.

#### Removed `baseUrl` Argument

The `baseUrl` argument has been removed from the `seoMeta` query. To rewrite absolute URLs for headless setups, configure your frontend domain in Statamic's sites configuration (`resources/sites.yaml`) instead.

```diff
  {
-   seoMeta(id: "...", baseUrl: "https://frontend.example.com") {
+   seoMeta(id: "...") {
      computed { canonical, hreflang, site_schema, breadcrumbs }
    }
  }
```

#### Computed Field Changes

The following fields have been removed from the `computed` type:

| Removed Field      | Use Instead              |
| ------------------ | ------------------------ |
| `title`            | Raw data `title` field   |
| `og_title`         | Raw data `og_title` field |
| `twitter_title`    | Raw data `og_title` field |
| `twitter_image`    | Raw data `og_image` field |
| `og_image`         | Raw data `og_image` field |
| `generated_og_image` | Raw data `og_image` field |

A new `twitter_card` field has been added to the `computed` type, returning the card size (`summary` or `summary_large_image`).

The following fields have been removed from the `seoMeta` raw type, `collectionSet`, and `taxonomySet` types:

| Removed Field                 | Use Instead   |
| ----------------------------- | ------------- |
| `site_name_position`          | Composed into `title` |
| `twitter_card`                | `seoMeta { computed { twitter_card } }` |
| `twitter_title`               | `og_title`    |
| `twitter_description`         | `og_description` |
| `twitter_summary_image`       | `og_image`    |
| `twitter_summary_large_image` | `og_image`    |

The following fields have been removed from the `siteSet` type:

| Removed Field                 | Use Instead   |
| ----------------------------- | ------------- |
| `twitter_summary_image`       | `og_image`    |
| `twitter_summary_large_image` | `og_image`    |

The `title_separator` field on the `siteSet` type has been renamed to `separator`.

#### `seoSitemaps` Query

The `seoSitemaps` query has been completely restructured for a simpler, flatter API:

{% tabs %}
{% tab title="Before (Advanced SEO 2.0)" %}
```graphql
{
  seoSitemaps {
    collection(baseUrl: "https://frontend.example.com", site: "default", handle: "pages") {
      loc
      lastmod
      alternates { hreflang, href }
    }
  }
}
```
{% endtab %}

{% tab title="After (Advanced SEO 3.0)" %}
```graphql
{
  seoSitemaps(site: "default", type: "collection", handle: "pages") {
    id
    type
    handle
    lastmod
    urls {
      loc
      lastmod
      changefreq
      priority
      alternates { hreflang, href }
    }
  }
}
```
{% endtab %}
{% endtabs %}

Key changes:

* `site` is now a required query argument (was optional on nested fields)
* `type` argument (`collection`, `taxonomy`, `custom`) replaces the nested fields
* `baseUrl` has been removed. Configure your frontend domain in `resources/sites.yaml` instead.
* Returns a list of sitemaps with `id`, `type`, `handle`, `lastmod`, and nested `urls` array

### Custom Sitemap Registration

Affects users who registered custom sitemaps.

The custom sitemaps API has been reworked. Closure-based registration via `Sitemap::register()` is no longer supported. Custom sitemaps now explicitly declare their site using the the `$site` property, and the sitemap index automatically includes them. The site defaults to the default Statamic site if not set.

#### Inline Sitemaps

Drop the `Sitemap::register()` closure wrapper and call `register()` directly on the sitemap:

```diff
- Sitemap::register(function () {
-     $signIn = Sitemap::makeUrl('https://example.com/sign-in');
-     $pricing = Sitemap::makeUrl('https://example.com/pricing')
-         ->changefreq('weekly')
-         ->priority('0.8');
-
-     return Sitemap::make('marketing')
-         ->add($signIn)
-         ->add($pricing);
- });
+ Sitemap::make('marketing')
+     ->site('english')
+     ->add('https://example.com/sign-in')
+     ->add('https://example.com/pricing', fn (CustomSitemapUrl $url) => $url
+         ->changefreq('weekly')
+         ->priority('0.8'))
+     ->register();
```

#### Class-Based Sitemaps

Class-based sitemaps now extend `CustomSitemap` instead of `BaseSitemap`. Define the `$handle` and `$site` as properties and use `$this->makeUrl()` instead of the facade:

```diff
- use Aerni\AdvancedSeo\Sitemaps\BaseSitemap;
+ use Aerni\AdvancedSeo\Sitemaps\Custom\CustomSitemap;

- class MarketingSitemap extends BaseSitemap
+ class MarketingSitemap extends CustomSitemap
  {
+     protected string $handle = 'marketing';
+
+     protected string $site = 'english';
+
      public function urls(): Collection
      {
          return collect([
-             Sitemap::makeUrl('https://example.com/sign-in'),
+             $this->makeUrl('https://example.com/sign-in'),
          ]);
      }
  }
```

You can register class-based sitemaps in a service provider or via the config:

```php
// In a service provider's boot method
MarketingSitemap::register();

// Or in config/advanced-seo.php
'sitemap' => [
    'custom' => [
        App\Sitemaps\MarketingSitemap::class,
    ],
],
```

See [Custom Sitemaps](../extending/sitemaps.md) for the full API reference.

### Events

Affects users listening for `SeoDefaultSetSaved`.

The `SeoDefaultSetSaved` event has been renamed to `SeoSetLocalizationSaved`. The public property has been renamed from `$defaults` to `$localization`.

New events have been added:

* `SeoSetConfigSaved` — Fired when a set's configuration is saved
* `SeoSetConfigDeleted` — Fired when a set's configuration is deleted
* `SeoSetLocalizationDeleted` — Fired when a localization is deleted

## Low Impact Changes

### Custom Views

Affects users who published or customized the `_twitter.antlers.html` snippet.

Update the following references:

| Old Variable              | New Variable      |
| ------------------------- | ----------------- |
| `seo:twitter_title`       | `seo:og_title`    |
| `seo:twitter_description` | `seo:og_description` |
| `seo:twitter_image`       | `seo:og_image`    |
| `seo:twitter_image:alt`   | `seo:og_image:alt` |

### Unified Social Image Field

Affects users who published or customized views referencing `seo:generated_og_image`.

The `seo_generated_og_image` field has been removed. The `seo_og_image` field now handles both generated and user-defined images automatically based on the `seo_generate_social_images` toggle state.

When the toggle is enabled, `seo_og_image` returns the generated image. When disabled, it returns the user-defined image.

Update any views that reference `seo:generated_og_image` to use `seo:og_image`.

### Generator Config Changes

Affects users who published `config/advanced-seo.php` with the `generate_on_save` option.

The `generate_on_save` option has been removed from the `generator` config:

```diff
  'generator' => [
-     'generate_on_save' => true,
  ],
```

Remove this option from your published config file if present.

Social image generation now works as follows:

* **After saving** — Images are generated using Laravel's `defer()`, which runs after the response is sent (sync driver) or dispatches a queued job. Content hashing ensures images are only regenerated when content actually changes.
* **On demand** — If a generated image is missing, it will be regenerated on-the-fly on the next frontend request.

### Removed Twitter Image Generation

Affects users with custom social images generator themes.

The social images generator no longer generates separate Twitter images. Only the Open Graph image is generated. The `twitter_summary` and `twitter_summary_large_image` config presets are still used to resize the shared image for the Twitter meta tags via Glide, but no separate images are generated.

Remove the `twitter_summary.antlers.html` and `twitter_summary_large_image.antlers.html` templates from your custom themes. Only `open_graph.antlers.html` is used.

### Template Changes

Affects users whose social image templates reference the `$group` variable.

The `$group` variable has been removed from the data passed to social images generator templates. If your templates reference this variable, update them.

### Image Storage Directory

Affects users of the social images generator.

Generated social images are now stored in `social_images/collection-{handle}/` and `social_images/taxonomy-{handle}/` subdirectories instead of `social_images/{handle}/`. This adds support for taxonomy terms and avoids collisions between collections and taxonomies with the same handle.

Existing images in the old directory structure will not be migrated automatically. They will be regenerated in the new location on the next save or frontend request. You may delete the orphaned images in the old directories.

## Automated Changes

Everything in this section is handled by upgrade scripts. No action required. These changes are documented for reference.

### Permissions

The permission system has been simplified. Old granular permissions have been replaced with three new permissions:

* `configure seo` — Full access to all SEO settings, defaults, and content editing
* `edit seo defaults` — Edit collection and taxonomy defaults
* `edit seo content` — Access the SEO tab on entries and terms

{% hint style="info" %}
Advanced SEO permissions now layer on top of Statamic's native collection and taxonomy permissions:

* To **edit SEO defaults** for a collection or taxonomy, a user needs `edit seo defaults` (or `configure seo`) **and** either Statamic's `configure collections` / `configure taxonomies` or `edit {handle} entries` / `edit {handle} terms` permission.
* To **configure SEO settings** for a collection or taxonomy, a user needs `configure seo` **and** Statamic's `configure collections` / `configure taxonomies` permission.

We recommend reviewing your roles after upgrading to confirm users have the right combination of Advanced SEO and Statamic permissions.
{% endhint %}

{% hint style="success" %}
**Automated** — User role permissions are automatically migrated. All existing roles receive the `edit seo content` permission to maintain backward compatibility.
{% endhint %}

### Single-Site Data Structure

Single-site installations now use the same data structure as multi-site. Config and localization data are stored separately:

```diff
  content/seo/collections/pages.yaml
+ content/seo/collections/{site}/pages.yaml
```

{% hint style="success" %}
**Automated** — Your existing data is automatically migrated to the new structure, whether you use file-based storage or the Eloquent driver.
{% endhint %}

### Site SEO Sets

The five separate site SEO sets (`site::general`, `site::indexing`, `site::social_media`, `site::analytics`, `site::favicons`) have been consolidated into a single `site::defaults` set. Tabs now provide the UI grouping within a single publish form.

For file-based installations, the file structure has changed:

```diff
- content/seo/site/general.yaml
- content/seo/site/indexing.yaml
- content/seo/site/social_media.yaml
- content/seo/site/analytics.yaml
- content/seo/site/favicons.yaml
- content/seo/site/{site}/general.yaml
- content/seo/site/{site}/indexing.yaml
- content/seo/site/{site}/social_media.yaml
- content/seo/site/{site}/analytics.yaml
- content/seo/site/{site}/favicons.yaml
+ content/seo/site/defaults.yaml
+ content/seo/site/{site}/defaults.yaml
```

{% hint style="success" %}
**Automated** — Your existing config and localization data is automatically merged into the single `site::defaults` set.
{% endhint %}

### Disabled Collections and Taxonomies

The `disabled` configuration option has been removed from `config/advanced-seo.php`:

```diff
- 'disabled' => [
-     'collections' => [],
-     'taxonomies' => [],
- ],
```

Collections and taxonomies can now be disabled individually through the Control Panel by clicking the "Configure" button on each set.

{% hint style="success" %}
**Automated** — Your existing disabled collections and taxonomies are automatically migrated. You can safely remove the `disabled` option from your published config file.
{% endhint %}

### Localization Origin Field

The `origin` field has been removed from localizations. Origin configuration is now stored centrally in the set config using an `origins` array.

{% hint style="success" %}
**Automated** — Your existing origin configuration is automatically migrated from localizations to the set config.
{% endhint %}

### Eloquent Driver

If you're using the Eloquent driver, the database schema has changed:

The `advanced_seo_defaults` table has been replaced by `seo_set_localizations`. A new `seo_set_configs` table has been added to store configuration data (enabled state, origins, etc.).

{% hint style="success" %}
**Automated** — New migrations are published and run automatically. Data is migrated from the old table to the new tables, and the old `advanced_seo_defaults` table is dropped.
{% endhint %}

### SEO Field Values

#### Simplified Field UI

The SEO source fields have been simplified from a three-state toggle (Auto/Default/Custom) to a two-state inheritance model. Fields are now either **inherited** (showing the default value) or **custom** (user-set value). The toggle has been removed. Transitioning between states now happens implicitly through editing and a Reset action.

#### Field Handle Syntax Replaced

The `@field:handle` syntax for referencing other fields has been replaced with standard Antlers `{{ handle }}` syntax. You can now use the full power of Antlers in your SEO fields, including modifiers like `{{ content | truncate(90, '...') }}`.

{% hint style="success" %}
**Automated** — All `@field:handle` references, as well as `@auto` and `@null` sentinel values, are automatically migrated.
{% endhint %}

#### Site Name Position Composed into Title

The `seo_site_name_position` field has been removed. Its value is now composed directly into the `seo_title` field using Antlers syntax, giving you full control over the title format.

| Position   | Resulting `seo_title`                              |
| ---------- | -------------------------------------------------- |
| `start`    | `{{ site_name }} {{ separator }} {{ title }}`      |
| `end`      | `{{ title }} {{ separator }} {{ site_name }}`      |
| `disabled` | `{{ title }}`                                      |

{% hint style="success" %}
**Automated** — The position is automatically composed into the title during the upgrade.
{% endhint %}

#### Title Separator Renamed

The `title_separator` field in site defaults has been renamed to `separator`.

{% hint style="success" %}
**Automated** — Existing `title_separator` values are automatically renamed to `separator` across all site localizations.
{% endhint %}

### X (Twitter) Social Sharing

The X (Twitter) social sharing has been significantly simplified. Instead of maintaining separate Twitter-specific fields, the addon now uses a unified social image shared between Open Graph and Twitter, with only the card size remaining as a Twitter-specific setting.

#### Removed Fields

The following fields have been removed from entries, terms, and SEO set localizations:

| Removed Field                    | Replacement      |
| -------------------------------- | ---------------- |
| `seo_twitter_title`              | Uses `seo_og_title` |
| `seo_twitter_description`        | Uses `seo_og_description` |
| `seo_twitter_summary_image`      | Uses `seo_og_image` |
| `seo_twitter_summary_large_image`| Uses `seo_og_image` |

The following fields have been removed from the site-wide social media defaults:

| Removed Field                    | Replacement      |
| -------------------------------- | ---------------- |
| `twitter_summary_image`          | Uses `og_image`  |
| `twitter_summary_large_image`    | Uses `og_image`  |

{% hint style="success" %}
**Automated** — Existing Twitter field data is automatically removed from your content during the upgrade.
{% endhint %}

#### Twitter Card Setting

The `seo_twitter_card` field has been moved from a per-localization setting to a per-collection/taxonomy configuration setting. It is now available in the "Social Appearance" section of the collection/taxonomy config.

{% hint style="success" %}
**Automated** — The existing `seo_twitter_card` value from the default site's localization is automatically migrated to the collection/taxonomy config.
{% endhint %}

### Social Images Generator

#### Per-Collection Settings

The `social_images_generator_collections` field has been removed from the site defaults. The social images generator is now enabled per-collection using the `social_images_generator` toggle in the collection config.

{% hint style="success" %}
**Automated** — Your existing settings are automatically migrated to per-collection configuration.
{% endhint %}

#### Removed Social Image Preview Targets

The social images generator no longer registers live preview targets for entries. Social image previews are now shown directly in the publish form via a new inline preview fieldtype with real-time theme switching.

### Sitemaps

#### Per-Collection/Taxonomy Settings

The `excluded_collections` and `excluded_taxonomies` fields have been removed from the site defaults. The sitemap is now enabled per-collection/taxonomy using the `sitemap` toggle in the collection/taxonomy config.

{% hint style="success" %}
**Automated** — Your existing settings are automatically migrated to per-collection/taxonomy configuration.
{% endhint %}

#### Domain Scoping

Each domain now gets its own sitemap index, and its sitemaps only contain URLs from sites on that domain. Previously, a single sitemap index included URLs from all sites regardless of domain.

| Setup                                                  | Sitemap indexes                                         |
| ------------------------------------------------------ | ------------------------------------------------------- |
| `example.com`, `example.com/de`, `example.com/fr`      | One index on `example.com` (unchanged)                  |
| `example.com`, `example.com/de`, `example.fr`          | One index on `example.com`, one on `example.fr`         |
| `example.com`, `example.de`, `example.fr`              | One index per domain                                    |

{% hint style="info" %}
Hreflang tags are unaffected and continue to reference all localized versions across domains.
{% endhint %}
