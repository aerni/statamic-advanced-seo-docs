---
description: >-
  The GraphQL API is a great way to integrate Advanced SEO with your headless
  front end.
---

# GraphQL

## Enable GraphQL

Enable GraphQL in the addon's config:

```php
'graphql' => true,
```

## SEO Fields

The Advance SEO data of your entries and terms is separated into three distinct fields:

| Fields     | Description                                                          |
| ---------- | -------------------------------------------------------------------- |
| `computed` | Contains computed fields like the `title`, `hreflang`, or `indexing` |
| `raw`      | Contains the augmented data of the SEO blueprint fields              |
| `views`    | Contains the rendered Advanced SEO `head` and `body` views           |

The easiest way to get the data is to use the `seo` field on Statamic's existing `entries`, `entry`, `terms`, and `term` queries:&#x20;

```graphql
{
  entry(id: "1d366a68-855e-4145-81ea-87174826a4eb") {
    seo {
      computed {
        title
      }
      raw {
        description
      }
      views {
        body
      }
    }
  }
}
```

## Available Queries

### SEO Meta

The `seoMeta` query is an alternative to using the `seo` field on the `entries`, `entry`, `terms`, and `term` queries.

| Argument | Type      | Description                                                                                                                                                                                               |
| -------- | --------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`     | `String!` | The ID of the entry/term to query                                                                                                                                                                         |
| `site`   | `String`  | Specify the site of your entry/term. This is only necessary when dealing with a term, as the term's ID isn't unique per site. But it can also be helpful if you want to get an entry in a different site. |

Get the computed title of an entry:

```graphql
{
  seoMeta(id: "1d366a68-855e-4145-81ea-87174826a4eb") {
    computed {
      title
    }
  }
}
```

Get the computed title of the `brands` term of the `tags` taxonomy in the `german` site:

```graphql
{
  seoMeta(id: "tags::brands", site: "german") {
    computed {
      title
    }
  }
}
```

### SEO Defaults

Use the `seoDefaults` query to get the default data. The query consists of three fields:

| Fields       | Description                                               |
| ------------ | --------------------------------------------------------- |
| `site`       | Get site defaults like `site_name` and default `og_image` |
| `collection` | Get the defaults of a specific collection                 |
| `taxonomy`   | Get the defaults of a specific taxonomy                   |

#### Site Defaults

| Argument | Type     | Description                           |
| -------- | -------- | ------------------------------------- |
| `site`   | `String` | Specify the site of the returned data |

Get the `site_name` from the `general` site defaults:

```graphql
{
  seoDefaults {
    site {
      general {
        site_name
      }
    }
  }
}
```

Get the data from the `german` site:

```graphql
{
  seoDefaults {
    site(site: "german") {
      general {
        site_name
      }
    }
  }
}
```

#### Collection Defaults

| Argument | Type      | Description                           |
| -------- | --------- | ------------------------------------- |
| `handle` | `String!` | The handle of the collection to query |
| `site`   | `String`  | Specify the site of the returned data |

Get the `title` from the `pages` collection defaults:

```graphql
{
  seoDefaults {
    collection(handle: "pages") {
      title
    }
  }
}
```

Get the data from the `german` site:

```graphql
{
  seoDefaults {
    collection(handle: "pages", site: "german") {
      title
    }
  }
}
```

#### Taxonomy Defaults

| Argument | Type      | Description                           |
| -------- | --------- | ------------------------------------- |
| `handle` | `String!` | The handle of the taxonomy to query   |
| `site`   | `String`  | Specify the site of the returned data |

Get the `title` from the `tags` taxonomy defaults:

```graphql
{
  seoDefaults {
    taxonomy(handle: "tags") {
      title
    }
  }
}
```

Get the data from the `german` site:

```graphql
{
  seoDefaults {
    taxonomy(handle: "tags", site: "german") {
      title
    }
  }
}
```

### SEO Sitemaps

Use the `seoSitemaps` query to get the data of your collection, taxonomy, and custom sitemaps. The query consists of three fields sharing the same structure:

| Fields       | Description                 |
| ------------ | --------------------------- |
| `collection` | Get the collection sitemaps |
| `taxonomy`   | Get the taxonomy sitemaps   |
| `custom`     | Get the custom sitemaps     |

Get the `loc` of all collection, taxonomy, and custom sitemaps:

```graphql
{
  seoSitemaps {
    collection {
      loc
    }
    taxonomy {
      loc
    }
    custom {
      loc
    }
  }
}
```

| Argument  | Type     | Description                                                                     |
| --------- | -------- | ------------------------------------------------------------------------------- |
| `baseUrl` | `String` | Change the base URL of your sitemap data.                                       |
| `handle`  | `String` | Filter the sitemaps by handle of their collection, taxonomy, or custom sitemap. |
| `site`    | `String` | Filter the data by site.                                                        |
