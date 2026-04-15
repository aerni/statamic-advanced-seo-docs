---
description: Edit SEO fields on individual entries and terms with dynamic tokens, inline previews, and AI copywriting.
---

# On-Page SEO

## SEO Tab

Advanced SEO adds an **SEO** tab to the blueprints of your collections and taxonomies. This is where you can edit all SEO-related fields for individual entries and terms.

The tab is organized into collapsible sections:

* **Search Appearance** — Meta title, description, and a live search preview.
* **Social Appearance** — OG title, description, image, social images generator settings, and a live social preview.
* **Indexing** — Noindex and nofollow toggles.
* **Canonical URL** — Point to the current page, another entry, or a custom URL. Hidden when noindex is enabled.
* **Sitemap** — Sitemap inclusion toggle.
* **Structured Data** — Custom JSON-LD.

### Visibility

There are two ways to control who sees the SEO tab:

* **Editable toggle** — Hide the SEO tab entirely for a collection or taxonomy via its [configuration](settings-and-defaults.md#configuration). Enabled by default. Useful to disable for content types where SEO is fully driven by defaults, like products or automated pages.
* **Permissions** — Control which users can view and edit SEO data via [permissions](permissions.md). This lets you restrict access per user role while keeping the tab available for authorized users.

## Field Values & Inheritance

Every SEO field on an entry or term inherits its value from the [defaults](settings-and-defaults.md) you've configured. When you edit a field, you override the inherited default. Use the **Reset to default** button below the field to revert to the inherited value. See [Cascade](settings-and-defaults.md#cascade) for how values are resolved.

## Token Input

Titles and descriptions use a token-based input field. Tokens are dynamic placeholders that resolve to field values at render time.

### Adding Tokens

Type `/` or click the `+` button in any title or description field to open the token autocomplete, then select a field to insert it as a token. Since tokens use Antlers syntax and support modifiers, you can also type Antlers directly into the field.

```
{{ title }}
{{ title | upper }}
{{ content | truncate(90, '...') }}
```

Tokens render in one of three visual states so you can tell at a glance what kind of value is attached:

* **Blue chip** — a known field token from the entry's blueprint or a registered [custom value token](../extending/tokens.md#value-tokens). Shows the field's display label.
* **Gray chip** — an Antlers variable the system doesn't recognize as a registered token. This could be an unregistered custom Antlers tag (`{{ company_name }}`), a nested field reference (`{{ foo:bar }}`), or a typo. The variable still resolves correctly on the frontend if it's valid Antlers.
* **Gray chip with a `{}` marker** — a variable with modifiers attached (`{{ title | upper }}`). The `{}` marker signals there's more going on than the name suggests. Double-click to see the full source.

### Editing Tokens

Double-click an existing token (or press Enter when focused) to edit it. This is useful for adding or adjusting Antlers modifiers, or changing the referenced field.

{% hint style="info" %}
Complex Antlers like tag pairs and conditionals is technically supported (it renders correctly on the frontend via `Antlers::parse()`), but isn't supported by the editor UI. Such expressions appear as multiple disjointed chips and plain text rather than a single unit, and editing them inline is unreliable. If you need dynamic values from a tag, register a [custom value token](../extending/tokens.md#value-tokens) instead. It'll appear as a proper blue chip in the autocomplete.
{% endhint %}

The token input shows a character counter with recommended limits. The counter only works with plain text and simple tokens. Custom Antlers with modifiers can't be counted as the final value isn't known at edit time:

| Field | Character Limit |
| ----- | --------------- |
| Meta Title | 60 |
| Meta Description | 160 |
| OG Title | 70 |
| OG Description | 200 |

Available tokens are derived from the entry or term's blueprint fields. You can also register [custom tokens](../extending/tokens.md) for additional fieldtypes or custom values.

{% hint style="info" %}
In the content defaults of a collection or taxonomy, the available field tokens are limited to fields that exist in **all** of its blueprints. If a collection has two blueprints and a field only exists in one of them, that field won't be available as a token in the defaults. This ensures the token resolves consistently for every entry or term, regardless of which blueprint it uses.
{% endhint %}

## Antlers in SEO Fields

Most SEO fields are designed for referencing other fields on your entry, e.g. `{{ title }}`, `{{ intro }}`, or chaining common modifiers like `{{ title | upper }}` or `{{ intro | truncate:160 }}`. The JSON-LD field goes further and often uses Antlers tags (`{{ collection }}`, `{{ nav }}`, `{{ glide }}`, loops, etc.) to build custom schemas.

Since Statamic 6.4.0, field content is parsed in Antlers' [user-content mode](https://github.com/statamic/cms/pull/14092), which restricts which tags and modifiers are available:

* **Variables always resolve** — `{{ any_field_handle }}` works everywhere.
* **Common modifiers work out of the box** — Statamic ships a generous default allowlist (`upper`, `lower`, `truncate`, `limit`, `markdown`, `format`, and many more). Your app's own custom modifiers are auto-allowed too.
* **Core tags are mostly restricted by default** — only a handful (`trans`, `widont`, `link`, and a few others) are allowed. Your app's own custom tags are auto-allowed.

If a tag or modifier you use in an SEO field renders nothing or logs a `Runtime Access Violation`, opt into it in `config/statamic/antlers.php`:

```php
'allowedContentTags' => ['@default', 'collection:*', 'glide:*'],
'allowedContentModifiers' => ['@default', 'raw', 'some_modifier'],
```

The `@default` token preserves Statamic's built-in defaults. Tag patterns use `name:*`; modifiers are listed by literal name.

## Search & Social Previews

The SEO tab includes inline previews that update in real time as you edit:

* **Search Preview** — Shows how the entry or term might appear in Google search results, including the title, description, URL, and breadcrumbs.
* **Social Preview** — Shows how the entry or term will appear when shared on Facebook and X (Twitter). The X (Twitter) preview adjusts based on the card setting configured on the collection or taxonomy.

{% hint style="info" %}
Previews are approximate. The actual appearance may vary depending on the platform, device, and other factors.
{% endhint %}

## AI Copywriting

Meta titles, meta descriptions, OG titles, and OG descriptions can be generated by AI directly from the SEO tab. See [AI Copywriting](ai.md) for setup, per-collection control, and instructions.
