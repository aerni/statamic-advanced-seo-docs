---
description: >-
  Upgrade from Advanced SEO v2 to v3. Most breaking changes are handled by
  automated upgrade scripts.
---

# Upgrade from v2

v3 introduces a significant internal refactor that improves the architecture, maintainability, and user experience of the addon. Most breaking changes have automated upgrade scripts, so the upgrade should be seamless for most users.

## Requirements

* PHP 8.4+
* Laravel 12+
* Statamic 6+

## Upgrade Summary

| Change                                                                        | Action                         |
| ----------------------------------------------------------------------------- | ------------------------------ |
| [Permissions](upgrade.md#permissions)                                         | ✅ Automated                    |
| [Single-site data structure](upgrade.md#single-site-data-structure)           | ✅ Automated                    |
| [Site SEO sets consolidated](upgrade.md#site-seo-sets)                        | ✅ Automated                    |
| [Disabled config removed](upgrade.md#disabled-collections-and-taxonomies)     | ✅ Automated                    |
| [Origin field centralized](upgrade.md#localization-origin-field)              | ✅ Automated                    |
| [Eloquent schema](upgrade.md#eloquent-driver)                                | ✅ Automated                    |
| [Simplified field UI](upgrade.md#simplified-field-ui)                         | ✅ Automated                    |
| [`@field:handle` syntax](upgrade.md#field-handle-syntax-replaced)             | ✅ Automated                    |
| [Site name position](upgrade.md#site-name-position-composed-into-title)       | ✅ Automated                    |
| [Title separator renamed](upgrade.md#title-separator-renamed)                 | ✅ Automated                    |
| [Twitter fields simplified](upgrade.md#removed-fields)                        | ✅ Automated                    |
| [Twitter card moved to config](upgrade.md#twitter-card-setting)               | ✅ Automated                    |
| [Twitter custom views](upgrade.md#custom-views)                               | ⚠️ Update published views      |
| [Screenshot package](upgrade.md#screenshot-package)                           | ⚠️ Require package manually     |
| [Social images per-collection](upgrade.md#per-collection-settings)            | ✅ Automated                    |
| [Generator config changes](upgrade.md#generator-config-changes)              | ⚠️ Remove from published config |
| [Twitter image generation removed](upgrade.md#removed-twitter-image-generation) | ⚠️ Remove custom templates   |
| [Social images template `$group`](upgrade.md#template-changes)               | ⚠️ Update custom templates     |
| [Social images directory](upgrade.md#image-storage-directory)                 | ⚠️ Delete orphaned images      |
| [Social image preview targets](upgrade.md#removed-social-image-preview-targets) | ✅ No changes needed          |
| [Unified social image field](upgrade.md#unified-social-image-field)           | ⚠️ Update published views      |
| [Sitemap per-collection/taxonomy](upgrade.md#per-collection-taxonomy-settings) | ✅ Automated                   |
| [Sitemap domain scoping](upgrade.md#domain-scoping)                          | ✅ No changes needed            |
| [Custom sitemap registration](upgrade.md#custom-sitemap-registration)        | ⚠️ Update code                 |
| [Events](upgrade.md#events)                                                  | ⚠️ Update event listeners      |
| [GraphQL](upgrade.md#graphql)                                                | ⚠️ Update queries              |
| [Blueprint customization removed](upgrade.md#blueprint-customization-removed) | ⚠️ Remove custom fields        |

## Permissions

The permission system has been simplified. Old granular permissions have been replaced with three new permissions:

* `configure seo` — Full access to all SEO settings, defaults, and content editing
* `edit seo defaults` — Edit collection and taxonomy defaults
* `edit seo content` — Access the SEO tab on entries and terms

{% hint style="info" %}
The `edit seo defaults` and `edit seo content` permissions work in conjunction with Statamic's native collection and taxonomy permissions. For example, to edit SEO defaults for the Pages collection, a user needs the `edit seo defaults` permission AND either Statamic's `configure collections` or `edit pages entries` permission.
{% endhint %}

{% hint style="success" %}
**Automated** — User role permissions are automatically migrated. All existing roles receive the `edit seo content` permission to maintain backward compatibility.
{% endhint %}

## Single-Site Data Structure

Single-site installations now use the same data structure as multi-site. Config and localization data are stored separately:

```diff
  content/seo/collections/pages.yaml
+ content/seo/collections/{site}/pages.yaml
```

{% hint style="success" %}
**Automated** — Your existing data is automatically migrated to the new structure, whether you use file-based storage or the Eloquent driver.
{% endhint %}

## Site SEO Sets

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

## Disabled Collections and Taxonomies

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

## Localization Origin Field

The `origin` field has been removed from localizations. Origin configuration is now stored centrally in the set config using an `origins` array.

{% hint style="success" %}
**Automated** — Your existing origin configuration is automatically migrated from localizations to the set config.
{% endhint %}

## Eloquent Driver

If you're using the Eloquent driver, the database schema has changed:

The `advanced_seo_defaults` table has been replaced by `seo_set_localizations`. A new `seo_set_configs` table has been added to store configuration data (enabled state, origins, etc.).

{% hint style="success" %}
**Automated** — New migrations are published and run automatically. Data is migrated from the old table to the new tables, and the old `advanced_seo_defaults` table is dropped.
{% endhint %}

## SEO Field Values

### Simplified Field UI

The SEO source fields have been simplified from a three-state toggle (Auto/Default/Custom) to a two-state inheritance model. Fields are now either **inherited** (showing the default value) or **custom** (user-set value). The toggle has been removed — transitioning between states happens implicitly through editing and a Reset action.

### Field Handle Syntax Replaced

The `@field:handle` syntax for referencing other fields has been replaced with standard Antlers `{{ handle }}` syntax. You can now use the full power of Antlers in your SEO fields, including modifiers like `{{ content | truncate(90, '...') }}`.

{% hint style="success" %}
**Automated** — All `@field:handle` references, as well as `@auto` and `@null` sentinel values, are automatically migrated.
{% endhint %}

### Site Name Position Composed into Title

The `seo_site_name_position` field has been removed. Its value is now composed directly into the `seo_title` field using Antlers syntax, giving you full control over the title format.

| Position   | Resulting `seo_title`                              |
| ---------- | -------------------------------------------------- |
| `start`    | `{{ site_name }} {{ separator }} {{ title }}`      |
| `end`      | `{{ title }} {{ separator }} {{ site_name }}`      |
| `disabled` | `{{ title }}`                                      |

{% hint style="success" %}
**Automated** — The position is automatically composed into the title during the upgrade.
{% endhint %}

### Title Separator Renamed

The `title_separator` field in site defaults has been renamed to `separator`.

{% hint style="success" %}
**Automated** — Existing `title_separator` values are automatically renamed to `separator` across all site localizations.
{% endhint %}

## X (Twitter) Social Sharing

The X (Twitter) social sharing has been significantly simplified. Instead of maintaining separate Twitter-specific fields, the addon now uses a unified social image shared between Open Graph and Twitter, with only the card size remaining as a Twitter-specific setting.

### Removed Fields

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

### Twitter Card Setting

The `seo_twitter_card` field has been moved from a per-localization setting to a per-collection/taxonomy configuration setting. It is now available in the "Social Appearance" section of the collection/taxonomy config.

{% hint style="success" %}
**Automated** — The existing `seo_twitter_card` value from the default site's localization is automatically migrated to the collection/taxonomy config.
{% endhint %}

### Custom Views

{% hint style="warning" %}
**Manual** — If you've published or customized the `_twitter.antlers.html` snippet, update the following references:
{% endhint %}

| Old Variable              | New Variable      |
| ------------------------- | ----------------- |
| `seo:twitter_title`       | `seo:og_title`    |
| `seo:twitter_description` | `seo:og_description` |
| `seo:twitter_image`       | `seo:og_image`    |
| `seo:twitter_image:alt`   | `seo:og_image:alt` |

## Social Images Generator

### Screenshot Package

The social images generator now uses [spatie/laravel-screenshot](https://spatie.be/docs/laravel-screenshot/v1/introduction) and is no longer bundled as a dependency. This package supports Browsershot and Cloudflare Browser Rendering as screenshot drivers.

To restore existing functionality, require both the screenshot package and Browsershot:

```shell
composer require spatie/laravel-screenshot spatie/browsershot
```

Alternatively, you can use Cloudflare Browser Rendering instead of Browsershot. Refer to the [installation guide](https://spatie.be/docs/laravel-screenshot/v1/installation-setup) for details. To customize screenshot settings, publish the config:

```shell
php artisan vendor:publish --tag=laravel-screenshot-config
```

{% hint style="warning" %}
**Manual** — If you were using the social images generator, run the `composer require` command above after upgrading to ensure the packages remain installed.
{% endhint %}

### Per-Collection Settings

The `social_images_generator_collections` field has been removed from the site defaults. The social images generator is now enabled per-collection using the `social_images_generator` toggle in the collection config.

{% hint style="success" %}
**Automated** — Your existing settings are automatically migrated to per-collection configuration.
{% endhint %}

### Generator Config Changes

The `generate_on_save` option has been removed from the `generator` config:

```diff
  'generator' => [
-     'generate_on_save' => true,
  ],
```

Social image generation now works as follows:

* **After saving** — Images are generated using Laravel's `defer()`, which runs after the response is sent (sync driver) or dispatches a queued job. Content hashing ensures images are only regenerated when content actually changes.
* **On demand** — If a generated image is missing, it will be regenerated on-the-fly on the next frontend request.

{% hint style="warning" %}
**Manual** — Remove this option from your published config file if present.
{% endhint %}

### Removed Twitter Image Generation

The social images generator no longer generates separate Twitter images. Only the Open Graph image is generated. The `twitter_summary` and `twitter_summary_large_image` config presets are still used to resize the shared image for the Twitter meta tags via Glide, but no separate images are generated.

{% hint style="warning" %}
**Manual** — If you have custom social images generator themes, remove the `twitter_summary.antlers.html` and `twitter_summary_large_image.antlers.html` templates — only `open_graph.antlers.html` is used.
{% endhint %}

### Template Changes

{% hint style="warning" %}
**Manual** — The `$group` variable has been removed from the data passed to social images generator templates. If your templates reference this variable, update them.
{% endhint %}

### Image Storage Directory

Generated social images are now stored in `social_images/collection-{handle}/` and `social_images/taxonomy-{handle}/` subdirectories instead of `social_images/{handle}/`. This adds support for taxonomy terms and avoids collisions between collections and taxonomies with the same handle.

{% hint style="warning" %}
**Manual** — Existing images in the old directory structure will not be migrated automatically. They will be regenerated in the new location on the next save or frontend request. You may delete the orphaned images in the old directories.
{% endhint %}

### Removed Social Image Preview Targets

The social images generator no longer registers live preview targets for entries. Social image previews are now shown directly in the publish form via a new inline preview fieldtype with real-time theme switching.

### Unified Social Image Field

The `seo_generated_og_image` field has been removed. The `seo_og_image` field now handles both generated and user-defined images automatically based on the `seo_generate_social_images` toggle state.

When the toggle is enabled, `seo_og_image` returns the generated image. When disabled, it returns the user-defined image.

{% hint style="warning" %}
**Manual** — If you published or customized views that reference `seo:generated_og_image`, update them to use `seo:og_image`.
{% endhint %}

## Sitemaps

### Per-Collection/Taxonomy Settings

The `excluded_collections` and `excluded_taxonomies` fields have been removed from the site defaults. The sitemap is now enabled per-collection/taxonomy using the `sitemap` toggle in the collection/taxonomy config.

{% hint style="success" %}
**Automated** — Your existing settings are automatically migrated to per-collection/taxonomy configuration.
{% endhint %}

### Domain Scoping

Each domain now gets its own sitemap index, and its sitemaps only contain URLs from sites on that domain. Previously, a single sitemap index included URLs from all sites regardless of domain.

| Setup                                                  | Sitemap indexes                                         |
| ------------------------------------------------------ | ------------------------------------------------------- |
| `example.com`, `example.com/de`, `example.com/fr`      | One index on `example.com` (unchanged)                  |
| `example.com`, `example.com/de`, `example.fr`          | One index on `example.com`, one on `example.fr`         |
| `example.com`, `example.de`, `example.fr`              | One index per domain                                    |

{% hint style="info" %}
Hreflang tags are unaffected and continue to reference all localized versions across domains.
{% endhint %}

### Custom Sitemap Registration

Due to domain scoping, the API for registering custom sitemaps has changed. Instead of `Sitemap::register()` which implicitly routed sitemaps based on their site, you now explicitly add sitemaps to a specific site's index:

```diff
- Sitemap::register($sitemap);
+ Sitemap::index('english')->add($sitemap);
```

{% hint style="warning" %}
**Manual** — Update any custom sitemap registration calls in your application code.
{% endhint %}

## Events

The `SeoDefaultSetSaved` event has been renamed to `SeoSetLocalizationSaved`. The public property has been renamed from `$defaults` to `$localization`.

New events have been added:

* `SeoSetConfigSaved` — Fired when a set's configuration is saved
* `SeoSetConfigDeleted` — Fired when a set's configuration is deleted
* `SeoSetLocalizationDeleted` — Fired when a localization is deleted

{% hint style="warning" %}
**Manual** — If you listen for `SeoDefaultSetSaved`, update your listener to use `SeoSetLocalizationSaved` and rename the `$defaults` property reference to `$localization`.
{% endhint %}

## GraphQL

The GraphQL API has been simplified and restructured for consistency. All GraphQL changes require updating your queries.

### `seoSet` Query

#### Renamed Query and Types

The query has been renamed from `seoDefaults` to `seoSet`:

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

#### Removed Fields

The following fields have been removed from the `siteSet` type as they are now configured per collection/taxonomy:

* `excluded_collections`
* `excluded_taxonomies`
* `social_images_generator_collections`

#### Disabled Features

Fields belonging to disabled features are now completely removed from the schema. Previously, disabled feature fields were still present but returned `null`. Now, if a feature is disabled in `config/advanced-seo.php`, its fields will not appear in the schema at all.

### `seoMeta` Query

#### Removed `baseUrl` Argument

The `baseUrl` argument has been removed. To rewrite absolute URLs for headless setups, configure your frontend domain in Statamic's sites configuration (`resources/sites.yaml`) instead.

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

### `seoSitemaps` Query

The query has been completely restructured for a simpler, flatter API:

{% tabs %}
{% tab title="Before (v2)" %}
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

{% tab title="After (v3)" %}
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
* `baseUrl` has been removed — configure your frontend domain in `resources/sites.yaml` instead
* Returns a list of sitemaps with `id`, `type`, `handle`, `lastmod`, and nested `urls` array

## Blueprint Customization Removed

Support for blueprint customization has been removed. This includes:

* The **Advanced SEO fieldsets** (e.g. `Advanced SEO (Main)`, `Title & Description`, `Open Graph`, etc.) that could be edited to customize which fields appear in the SEO tab
* The **Advanced SEO fieldtype** (`advanced_seo`) that allowed placing individual SEO fields anywhere in a blueprint

If you customized any of the Advanced SEO fieldsets, those customizations will no longer have any effect. If you used the `advanced_seo` fieldtype in your blueprints, those fields will no longer work.

{% hint style="warning" %}
**Manual** — Remove any `advanced_seo` fieldtype fields from your blueprints and any customizations to the Advanced SEO fieldsets. The SEO tab fields are now managed entirely by the addon and cannot be customized.
{% endhint %}
