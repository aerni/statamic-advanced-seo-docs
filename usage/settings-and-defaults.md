---
description: >-
  The SEO defaults consist of global settings and default values. They are
  organized into site, collection, and taxonomy defaults.
---

# Settings & Defaults



## SEO Navigation

Advanced SEO will add a `SEO` section to Statamic's sidebar navigation. This section is home to all the global settings and default values.

<figure><img src="../.gitbook/assets/CleanShot 2023-03-01 at 14.21.24@2x.png" alt=""><figcaption></figcaption></figure>

## Site

The site defaults consist of thoughtfully organized site-wide settings like the site name, indexing, social images, and analytics trackers.&#x20;

<figure><img src="../.gitbook/assets/site_defaults.png" alt=""><figcaption><p>The overview of the site defaults.</p></figcaption></figure>

## Collection & Taxonomy

The collection and taxonomy defaults let you define default values for your entries and terms like title, description, and social images.

<figure><img src="../.gitbook/assets/collection_defaults.png" alt=""><figcaption><p>The overview of the collection defaults.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/taxonomy_defaults.png" alt=""><figcaption><p>The overview of the taxonomy defaults.</p></figcaption></figure>

## Stache Store

You may configure the directory of the stache store by editing the path of the `directory` config:

```php
'directory' => base_path('content/seo'),
```
