---
description: Control which users can view and edit SEO settings, defaults, and content.
---

# Permissions

{% hint style="info" %}
Granular permissions require the [Pro edition](../getting-started/editions.md).
{% endhint %}

Advanced SEO adds three permissions under the **Advanced SEO** group:

| Permission | Description |
| ---------- | ----------- |
| `configure seo` | Full access to all SEO settings, defaults, configuration, and the SEO tab on entries and terms. |
| `edit seo defaults` | Edit collection and taxonomy defaults, and access the SEO tab on entries and terms. |
| `edit seo content` | Access the SEO tab on entries and terms. |

Super users always have full access regardless of permissions.

## How Permissions Work

### Site Defaults

Only users with the `configure seo` permission can edit site defaults.

### Collection & Taxonomy Defaults

Users need the `configure seo` or `edit seo defaults` permission **and** Statamic's native permission for the corresponding content type. For example, to edit SEO defaults for the Pages collection, a user needs `edit seo defaults` plus either Statamic's `configure collections` or `edit pages entries` permission. Accessing the configuration requires `configure seo` and the native `configure collections` or `configure taxonomies` permission.

### SEO Tab on Entries & Terms

Any of the three permissions grants access to the SEO tab. Additionally, the collection or taxonomy must have its [Editable](settings-and-defaults.md#configuration) toggle enabled.
