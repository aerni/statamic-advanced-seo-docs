---
description: Edit SEO fields on individual entries and terms with dynamic tokens, inline previews, and AI content generation.
---

# On-Page SEO

## SEO Tab

Advanced SEO adds an **SEO** tab to the blueprints of your collections and taxonomies. This is where you can edit all SEO-related fields for individual entries and terms.

The tab is organized into collapsible sections:

* **Search Appearance** — Meta title, description, and a live search preview.
* **Social Appearance** — OG title, description, image, social images generator settings, and a live social preview.
* **Indexing** — Noindex and nofollow toggles.
* **Canonical URL** — Point to the current page, another entry, or a custom URL. Hidden when noindex is enabled.
* **Sitemap** — Sitemap inclusion, priority, and change frequency.
* **Structured Data** — Custom JSON-LD.

### Visibility

There are two ways to control who sees the SEO tab:

* **Editable toggle** — Hide the SEO tab entirely for a collection or taxonomy via its [configuration](settings-and-defaults.md#configuration). Enabled by default. Useful to disable for content types where SEO is fully driven by defaults, like products or automated pages.
* **Permissions** — Control which users can view and edit SEO data via [permissions](permissions.md). This lets you restrict access per user role while keeping the tab available for authorized users.

## Field Values & Inheritance

Every SEO field on an entry or term inherits its value from the [defaults](settings-and-defaults.md) you've configured. When you edit a field, you override the inherited default. Use the **Reset to default** button below the field to revert to the inherited value. See [Cascade](settings-and-defaults.md#cascade) for how values are resolved.

## Token Input

Titles and descriptions use a token-based input field. Tokens are dynamic placeholders that resolve to field values at render time.

### Using Tokens

Type `/` or click the `+` button in any title or description field to open the token autocomplete. Select a field from the dropdown to insert it as a token. Double-click an existing token to edit it. Tokens use Antlers syntax and support modifiers:

```
{{ title }}
{{ title | upper }}
{{ content | truncate(90, '...') }}
```

The token input shows a character counter with recommended limits. The counter only works with plain text and simple tokens. Custom Antlers with modifiers can't be counted as the final value isn't known at edit time:

| Field | Character Limit |
| ----- | --------------- |
| Meta Title | 60 |
| Meta Description | 160 |
| OG Title | 70 |
| OG Description | 200 |

Available tokens are derived from the entry or term's blueprint fields. You can also register [custom tokens](../extending/tokens.md) for additional fieldtypes or custom values.

## Search & Social Previews

The SEO tab includes inline previews that update in real time as you edit:

* **Search Preview** — Shows how the entry or term might appear in Google search results, including the title, description, URL, and breadcrumbs.
* **Social Preview** — Shows how the entry or term will appear when shared on Facebook and X (Twitter). The X (Twitter) preview adjusts based on the card setting configured on the collection or taxonomy.

{% hint style="info" %}
Previews are approximate. The actual appearance may vary depending on the platform, device, and other factors.
{% endhint %}

## AI Content Generation

{% hint style="info" %}
AI content generation requires the [Pro edition](../getting-started/editions.md).
{% endhint %}

Advanced SEO can generate titles and descriptions using AI. This requires the [Laravel AI SDK](https://github.com/laravel/ai) to be installed and configured.

### Setup

Run the install command and select **AI Content Generation**:

```shell
php please seo:install
```

This installs the [Laravel AI SDK](https://github.com/laravel/ai) and enables AI in your config. After installation, configure your AI provider in `config/ai.php`. Any provider supported by the Laravel AI SDK works (OpenAI, Anthropic, Google, etc.).

If you don't want Advanced SEO to use the default provider from `config/ai.php`, you can set a specific provider and model in `config/advanced-seo.php`:

```php
'ai' => [
    'provider' => 'anthropic',
    'model' => 'claude-haiku-4-5-20251001',
],
```

### Usage

When AI is enabled, a generate action appears in the token input fields. AI can generate:

* Meta titles
* Meta descriptions
* OG titles
* OG descriptions

The AI analyzes the entry or term's content to generate contextual text. It respects the site's language for multilingual generation.

{% hint style="info" %}
The entry or term needs at least 50 characters of content for AI generation to work.
{% endhint %}
