---
description: Configure site-wide SEO settings, collection and taxonomy defaults, and multi-site data inheritance.
---

# Settings & Defaults

Advanced SEO adds an **SEO** item to the tools section in the Control Panel sidebar. This is where you manage all your SEO settings and defaults.

There are two types of defaults:

1. **Site defaults** — Global settings that apply across your entire site
2. **Collection & Taxonomy defaults** — Default SEO values for each collection and taxonomy

## Cascade

SEO values are resolved through a cascade. Each level serves a different purpose:

* **Site defaults** — Global values like the site name, separator, default OG image, indexing rules, and analytics. These apply across the entire site.
* **Collection & Taxonomy defaults** — Field-level values like title templates, descriptions, and sitemap settings. These are inherited by entries and terms.
* **Entries & Terms** — Per-page overrides set by content editors.

Where fields exist at multiple levels (such as the OG image), values fall back upward: entries and terms fall back to collection and taxonomy defaults, which fall back to site defaults. Site-level noindex/nofollow is the exception: when enabled, it overrides everything below it.

When an editor sets a value on an entry or term, it overrides the inherited default. To revert to the inherited value, use the **Reset to default** button below the field. This removes the override and lets the value cascade down from the defaults again.

## Site Defaults

Site defaults are your global SEO settings. They provide site-wide values like the site name, separator, default social image, indexing rules, and analytics trackers.

The settings are organized into tabs:

* **Search Appearance** — Site name, title separator, structured data (JSON-LD), and favicons
* **Social Appearance** — Default OG image and X (Twitter) handle. The OG image cascades down to collection, taxonomy, entry, and term levels as the fallback image.
* **Indexing** — Global noindex/nofollow and site verification codes. When enabled, these override all collection, taxonomy, entry, and term values.
* **Analytics** — Fathom, Cloudflare Web Analytics, and Google Tag Manager

{% hint style="info" %}
Analytics scripts only render in the configured [crawling environments](configuration.md#analytics).
{% endhint %}

## Collection & Taxonomy Defaults

Each collection and taxonomy has its own set of default values. These are the values that entries and terms inherit when they don't define their own. The available fields mirror the [on-page SEO fields](on-page-seo.md) on entries and terms.

For example, you might set a default SEO title template of `{{ title }} {{ separator }} {{ site_name }}` on the Pages collection. Every page will use that template unless an editor overrides it on a specific entry.

### Configuration

Each collection and taxonomy also has a configuration that controls how Advanced SEO behaves for that content type. Access it by clicking the **Configure** button on any collection or taxonomy in the SEO section.

* **Enabled** — Toggle SEO processing on or off. When disabled, no SEO data is computed for its entries/terms and the SEO tab is hidden.
* **Editable** — Controls whether non-super-users see the SEO tab on entries/terms. Useful when you want SEO to be fully driven by defaults or computed values without giving editors control.
* **Origin** — Set the origin site for multi-site inheritance. See [Origins](#origins) below.
* **Sitemap** — Include this collection/taxonomy in the XML sitemap.
* **Twitter Card** — Choose between `summary` and `summary_large_image` for entries/terms.
* **Social Images Generator** — Enable automatic social image generation and restrict which themes are available.

## Origins

In a multi-site setup, origins control how SEO defaults are inherited across sites. Each site can inherit its defaults from another site, creating an inheritance chain.

For example, if your German site inherits from your English site, any SEO defaults not explicitly set for German will fall back to the English values. This means you only need to maintain one complete set of defaults and can selectively override values for other sites.
