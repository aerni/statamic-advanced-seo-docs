---
description: Query SEO metadata, defaults, and sitemaps using Advanced SEO's GraphQL API.
---

# GraphQL

## Enable GraphQL

Enable the GraphQL API in `config/advanced-seo.php`:

```php
'graphql' => true,
```

{% hint style="info" %}
Statamic's GraphQL API must also be enabled. Refer to the [Statamic documentation](https://statamic.dev/graphql) for setup.
{% endhint %}

{% hint style="info" %}
Fields belonging to disabled features are removed from the schema. For example, disabling the sitemap removes all sitemap-related fields, and disabling Fathom Analytics removes the Fathom fields.
{% endhint %}

## Queries

### seoMeta

Query the SEO metadata of a specific entry or term.

Returns a [seoMeta](#seometa) type.

| Argument | Type | Description |
| -------- | ---- | ----------- |
| `id` | `String!` | The entry or term ID. |
| `site` | `String` | Site handle. Without it, returns the entry or term in its origin localization. Returns `null` if the content doesn't exist in the given site. |

```graphql
{
  seoMeta(id: "entry-uuid-here", site: "default") {
    computed {
      canonical
      indexing
      locale
    }
    raw {
      title
      description
    }
    view {
      head
      body
    }
  }
}
```

See the [seoMeta](#seometa) type for all available fields.

### seoSet

Query the SEO defaults for the site, collections, and taxonomies.

Returns a [seoSet](#seoset) type.

```graphql
{
  seoSet {
    site(site: "default") {
      site_name
      separator
    }
    collection(handle: "pages", site: "default") {
      title
      description
    }
    taxonomy(handle: "tags", site: "default") {
      title
      description
    }
  }
}
```

See the [siteSet](#siteset), [collectionSet](#collectionset), and [taxonomySet](#taxonomyset) types for all available fields.

### seoSitemaps

Query the sitemaps for a specific site.

Returns a list of [Sitemap](#sitemap) types.

| Argument | Type | Description |
| -------- | ---- | ----------- |
| `site` | `String!` | Site handle. Returns sitemaps for all sites sharing that site's domain. |
| `type` | `SitemapType` | Filter by sitemap type: `COLLECTION`, `TAXONOMY`, or `CUSTOM`. |
| `handle` | `String` | Filter by collection, taxonomy, or custom sitemap handle. |

```graphql
{
  seoSitemaps(site: "default", type: COLLECTION, handle: "pages") {
    id
    type
    handle
    lastmod
    urls {
      loc
      lastmod
      changefreq
      priority
      alternates { href, hreflang }
    }
  }
}
```

## Fields

### seo

When GraphQL is enabled, an `seo` field is added to the `EntryInterface` and `TermInterface`. It returns a [seoMeta](#seometa) type.

```graphql
{
  entries {
    data {
      title
      seo {
        computed {
          canonical
          indexing
        }
        raw {
          title
          description
        }
      }
    }
  }
}
```

## Types

### seoMeta

| Field | Type | Description |
| ----- | ---- | ----------- |
| `computed` | [computedMetaData](#computedmetadata) | Resolved values after cascade processing. |
| `raw` | [rawMetaData](#rawmetadata) | Direct field values as stored on the entry or term. |
| `view` | [renderedViews](#renderedviews) | Pre-rendered HTML for the head and body. |

### computedMetaData

| Field | Type | Description |
| ----- | ---- | ----------- |
| `canonical` | `String` | The canonical URL. |
| `indexing` | `String` | The indexing directive (e.g. `noindex, nofollow`). |
| `locale` | `String` | The current locale. |
| `twitter_card` | `String` | The card type (`summary` or `summary_large_image`). |
| `twitter_handle` | `String` | The X (Twitter) handle. |
| `hreflang` | [[Hreflang](#hreflang)] | Alternate language versions. |
| `og_image_preset` | [SocialImagePreset](#socialimagepreset) | The OG image dimensions. |
| `twitter_image_preset` | [SocialImagePreset](#socialimagepreset) | The Twitter image dimensions. |
| `site_schema` | `String` | The site JSON-LD schema. |
| `breadcrumbs` | `String` | The JSON-LD breadcrumbs. |

### rawMetaData

The raw field values as stored on the entry or term. Field handles match the blueprint with the `seo_` prefix removed.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `title` | `String` | The meta title. |
| `description` | `String` | The meta description. |
| `og_title` | `String` | The Open Graph title. |
| `og_description` | `String` | The Open Graph description. |
| `og_image` | `String` | The Open Graph image. |
| `generate_social_images` | `Boolean` | Whether to generate social images. |
| `social_images_theme` | `String` | The social images theme. |
| `noindex` | `Boolean` | Whether the entry is noindexed. |
| `nofollow` | `Boolean` | Whether the entry is nofollowed. |
| `canonical_type` | `String` | The canonical URL type (`current`, `entry`, or `custom`). |
| `canonical_entry` | `String` | The canonical entry ID. |
| `canonical_custom` | `String` | The custom canonical URL. |
| `sitemap_enabled` | `Boolean` | Whether the entry is included in the sitemap. |
| `sitemap_priority` | `String` | The sitemap priority (`0.0`–`1.0`). |
| `sitemap_change_frequency` | `String` | The sitemap change frequency. |
| `json_ld` | `String` | The custom JSON-LD. |

### renderedViews

Pre-rendered HTML for the `<head>` and `<body>` sections. Only use this when your frontend is hosted on the same domain as Statamic, as the views contain absolute URLs.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `head` | `String` | The rendered head HTML. |
| `body` | `String` | The rendered body HTML. |

### seoSet

| Field | Type | Arguments | Description |
| ----- | ---- | --------- | ----------- |
| `site` | [siteSet](#siteset) | `site: String` | Site-wide defaults. Falls back to the default site. |
| `collection` | [collectionSet](#collectionset) | `handle: String!`, `site: String` | Collection defaults. Falls back to the default site. |
| `taxonomy` | [taxonomySet](#taxonomyset) | `handle: String!`, `site: String` | Taxonomy defaults. Falls back to the default site. |

### siteSet

The site-wide SEO defaults. Field handles match the site blueprint.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `site_name` | `String` | The site name. |
| `separator` | `String` | The title separator. |
| `site_json_ld_type` | `String` | The JSON-LD type (`none`, `organization`, `person`, or `custom`). |
| `use_breadcrumbs` | `Boolean` | Whether to use breadcrumbs. |
| `organization_name` | `String` | The organization name. |
| `organization_logo` | `String` | The organization logo. |
| `person_name` | `String` | The person name. |
| `site_json_ld` | `String` | The custom site JSON-LD. |
| `favicon_svg` | `String` | The SVG favicon. |
| `og_image` | `String` | The default Open Graph image. |
| `twitter_handle` | `String` | The X (Twitter) handle. |
| `noindex` | `Boolean` | Whether the site is noindexed. |
| `nofollow` | `Boolean` | Whether the site is nofollowed. |
| `google_site_verification_code` | `String` | The Google site verification code. |
| `bing_site_verification_code` | `String` | The Bing site verification code. |
| `use_fathom` | `Boolean` | Whether to use Fathom Analytics. |
| `fathom_id` | `String` | The Fathom site ID. |
| `fathom_spa` | `Boolean` | Whether to enable Fathom SPA mode. |
| `use_cloudflare_web_analytics` | `Boolean` | Whether to use Cloudflare Web Analytics. |
| `cloudflare_web_analytics` | `String` | The Cloudflare Web Analytics token. |
| `use_google_tag_manager` | `Boolean` | Whether to use Google Tag Manager. |
| `google_tag_manager` | `String` | The Google Tag Manager ID. |

### collectionSet

The SEO defaults for a collection. Field handles match the blueprint with the `seo_` prefix removed.

| Field | Type | Description |
| ----- | ---- | ----------- |
| `title` | `String` | The default meta title. |
| `description` | `String` | The default meta description. |
| `og_title` | `String` | The default Open Graph title. |
| `og_description` | `String` | The default Open Graph description. |
| `og_image` | `String` | The default Open Graph image. |
| `generate_social_images` | `Boolean` | Whether to generate social images. |
| `social_images_theme` | `String` | The social images theme. |
| `noindex` | `Boolean` | Whether to noindex entries. |
| `nofollow` | `Boolean` | Whether to nofollow entries. |
| `canonical_type` | `String` | The default canonical URL type. |
| `canonical_entry` | `String` | The default canonical entry ID. |
| `canonical_custom` | `String` | The default custom canonical URL. |
| `sitemap_enabled` | `Boolean` | Whether entries are included in the sitemap. |
| `sitemap_priority` | `String` | The default sitemap priority (`0.0`–`1.0`). |
| `sitemap_change_frequency` | `String` | The default sitemap change frequency. |
| `json_ld` | `String` | The default JSON-LD. |

### taxonomySet

The SEO defaults for a taxonomy. Has the same fields as [collectionSet](#collectionset).

### Sitemap

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | `String` | The sitemap identifier (e.g. `collection-pages`). |
| `type` | `String` | The sitemap type (`collection`, `taxonomy`, or `custom`). |
| `handle` | `String` | The collection, taxonomy, or custom sitemap handle. |
| `lastmod` | `String` | The last modification date. |
| `urls` | [[SitemapUrl](#sitemapurl)] | The URLs in the sitemap. |

### SitemapUrl

| Field | Type | Description |
| ----- | ---- | ----------- |
| `loc` | `String` | The URL location. |
| `lastmod` | `String` | The last modification date. |
| `changefreq` | `String` | The change frequency. |
| `priority` | `String` | The priority (`0.0`–`1.0`). |
| `alternates` | [[SitemapAlternates](#sitemapalternates)] | Hreflang alternates. |

### SitemapAlternates

| Field | Type | Description |
| ----- | ---- | ----------- |
| `href` | `String` | The alternate URL. |
| `hreflang` | `String` | The language code. |

### Hreflang

| Field | Type | Description |
| ----- | ---- | ----------- |
| `url` | `String` | The alternate language URL. |
| `locale` | `String` | The language locale code. |

### SocialImagePreset

| Field | Type | Description |
| ----- | ---- | ----------- |
| `width` | `String` | The image width. |
| `height` | `String` | The image height. |
