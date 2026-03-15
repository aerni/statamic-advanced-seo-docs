---
description: >-
  Configure site-wide SEO settings, collection and taxonomy defaults, and
  multi-site data inheritance.
---

# Settings & Defaults

Advanced SEO adds an **SEO** section to the Control Panel sidebar. This is where you manage site-wide settings, collection defaults, and taxonomy defaults.

## Site Defaults

Site defaults are global settings that apply across your entire site. They are organized into four tabs:

### Search Appearance

Configure how your site appears in search results.

**Display**

* **Site Name** — The name shown in search results and meta tags.
* **Separator** — The character between the page title and site name (e.g. `|`, `–`, `·`).

**Structured Data**

* **JSON-LD Type** — Choose between Organization or Person for your site's knowledge graph.
* **Breadcrumbs** — Enable breadcrumb structured data.
* **Organization/Person Details** — Name, logo, and other identity information.
* **Custom JSON-LD** — Add custom structured data.

**Favicons**

* **SVG Favicon** — Upload an SVG file to use as your site's favicon.

### Social Appearance

* **Default OG Image** — The fallback image used for social sharing when no page-specific image is set.
* **Twitter Handle** — Your site's X (Twitter) handle for the `twitter:site` meta tag.

### Indexing

* **Noindex / Nofollow** — Global indexing defaults.
* **Site Verification** — Google and Bing verification codes.

### Analytics

Configure analytics trackers. Each can be individually enabled or disabled in `config/advanced-seo.php`.

* **Fathom** — Site ID and SPA mode toggle.
* **Cloudflare Web Analytics** — Analytics token.
* **Google Tag Manager** — Container ID.

{% hint style="info" %}
Analytics scripts only render in the environments configured in `config/advanced-seo.php` under `analytics.environments` (defaults to `production`).
{% endhint %}

## Collection & Taxonomy Configuration

Each collection and taxonomy has its own configuration that controls how Advanced SEO behaves for that content type. Access it by clicking the **Configure** button on any collection or taxonomy in the SEO section.

### General

* **Enabled** — Toggle SEO processing on or off for this collection/taxonomy. When disabled, no SEO data is computed for its entries/terms.
* **Editable** — Controls whether non-super-users see the SEO tab on entries/terms. Useful when you want SEO fields to be computed from defaults without giving editors control.

### Origins

Configure which site's defaults a localization should inherit from. See [Origins](settings-and-defaults.md#origins) below.

### Sitemaps

* **Sitemap** — Include this collection/taxonomy in the sitemap.

### Social Appearance

* **Twitter Card** — Choose between `summary` and `summary_large_image` for entries/terms in this collection/taxonomy.
* **Social Images Generator** — Enable automatic social image generation.
* **Themes** — Restrict which social image themes are available for this collection/taxonomy.

## Collection & Taxonomy Defaults

Each collection and taxonomy has default values for SEO fields. These serve as fallbacks — when an entry or term doesn't set its own value, the default is used.

Defaults are organized into collapsible sections:

* **Search Appearance** — Default title and description templates using [token input](on-page-seo.md#token-input).
* **Social Appearance** — Default OG title, description, image, and social image generation settings.
* **Indexing** — Default noindex/nofollow settings.
* **Canonical URL** — Default canonical URL behavior.
* **Sitemap** — Default sitemap priority and change frequency.
* **Structured Data** — Default JSON-LD data.

{% hint style="info" %}
The data cascade flows: **Site defaults → Collection/Taxonomy defaults → Entry/Term values**. Each level overrides the one above it. See [Data Inheritance](on-page-seo.md#data-inheritance) for details.
{% endhint %}

## Origins

Origins control how SEO defaults are inherited across sites in a multi-site setup. Each site can inherit its defaults from another site, creating an inheritance chain.

For example, if your German site inherits from your English site, any SEO defaults not explicitly set for German will fall back to the English values.

Origins are configured per collection/taxonomy from the [configuration page](settings-and-defaults.md#collection--taxonomy-configuration).

{% hint style="warning" %}
Circular dependencies are not allowed. At least one site must be a root (no origin) to prevent infinite loops.
{% endhint %}

## Storage

By default, Advanced SEO stores its data in `content/seo/`. You can change this directory in `config/advanced-seo.php`:

```php
'directory' => base_path('content/seo'),
```
