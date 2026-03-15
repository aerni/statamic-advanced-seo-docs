---
description: >-
  Query SEO metadata, defaults, and sitemaps using Advanced SEO's GraphQL API.
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

## SEO Fields on Entries & Terms

When GraphQL is enabled, entries and terms receive an `seo` field that returns their resolved SEO metadata. This uses the same data as the `seoMeta` query.

## Queries

### `seoMeta`

Retrieve computed and raw SEO metadata for a specific entry or term.

**Arguments:**

| Argument | Type | Required | Description |
| -------- | ---- | -------- | ----------- |
| `id` | String | Yes | The entry or term ID |
| `site` | String | No | Site handle for localization |

**Example:**

```graphql
{
  seoMeta(id: "entry-uuid-here", site: "default") {
    computed {
      canonical
      indexing
      locale
      twitter_card
      twitter_handle
      hreflang { url, locale }
      og_image_preset { width, height }
      twitter_image_preset { width, height }
      site_schema
      breadcrumbs
    }
    raw {
      title
      description
      og_title
      og_description
      og_image
      noindex
      nofollow
      canonical_type
      sitemap_enabled
      sitemap_priority
      sitemap_change_frequency
      json_ld
    }
    view {
      head
      body
    }
  }
}
```

#### Fields

**`computed`** — Resolved values after cascade processing:

| Field | Type | Description |
| ----- | ---- | ----------- |
| `canonical` | String | Canonical URL |
| `indexing` | String | Indexing directive |
| `locale` | String | Current locale |
| `twitter_card` | String | Card type (`summary` or `summary_large_image`) |
| `twitter_handle` | String | X (Twitter) handle |
| `hreflang` | [Hreflang] | Alternate language versions |
| `og_image_preset` | Preset | OG image dimensions |
| `twitter_image_preset` | Preset | Twitter image dimensions |
| `site_schema` | String | JSON-LD schema |
| `breadcrumbs` | String | JSON-LD breadcrumbs |

**`raw`** — Direct field values as stored on the entry/term. Field handles match the blueprint without the `seo_` prefix.

**`view`** — Pre-rendered HTML:

| Field | Type | Description |
| ----- | ---- | ----------- |
| `head` | String | Rendered head HTML |
| `body` | String | Rendered body HTML |

### `seoSet`

Access SEO defaults for the site, collections, and taxonomies.

**Fields:**

| Field | Arguments | Description |
| ----- | --------- | ----------- |
| `site` | `site: String` | Site-wide defaults |
| `collection` | `handle: String!`, `site: String` | Collection defaults |
| `taxonomy` | `handle: String!`, `site: String` | Taxonomy defaults |

**Example — Site defaults:**

```graphql
{
  seoSet {
    site(site: "default") {
      site_name
      separator
      noindex
      nofollow
      og_image
      twitter_handle
      fathom_id
      favicon_svg
    }
  }
}
```

**Example — Collection defaults:**

```graphql
{
  seoSet {
    collection(handle: "pages", site: "default") {
      title
      description
      og_title
      og_description
      og_image
      noindex
      nofollow
    }
  }
}
```

{% hint style="info" %}
Fields belonging to disabled features are completely removed from the schema. For example, disabling the sitemap in config removes all sitemap-related fields.
{% endhint %}

### `seoSitemaps`

Retrieve sitemap data for a specific site.

**Arguments:**

| Argument | Type | Required | Description |
| -------- | ---- | -------- | ----------- |
| `site` | String | Yes | Site handle |
| `type` | SitemapType | No | Filter: `COLLECTION`, `TAXONOMY`, or `CUSTOM` |
| `handle` | String | No | Filter by handle |

**Example:**

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

**Sitemap fields:**

| Field | Type | Description |
| ----- | ---- | ----------- |
| `id` | String | Sitemap identifier (e.g. `collection-pages`) |
| `type` | String | `collection`, `taxonomy`, or `custom` |
| `handle` | String | The collection, taxonomy, or custom handle |
| `lastmod` | String | Last modification date |
| `urls` | [SitemapUrl] | URLs in the sitemap |

**URL fields:**

| Field | Type | Description |
| ----- | ---- | ----------- |
| `loc` | String | URL location |
| `lastmod` | String | Last modification date |
| `changefreq` | String | Change frequency |
| `priority` | String | Priority (0.0–1.0) |
| `alternates` | [Alternate] | Hreflang alternates |
