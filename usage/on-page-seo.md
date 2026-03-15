---
description: >-
  Edit SEO fields on individual entries and terms with dynamic tokens,
  inline previews, and AI content generation.
---

# On-Page SEO

## SEO Tab

Advanced SEO adds an **SEO** tab to the blueprints of your collections and taxonomies. This is where you can edit all SEO-related fields for individual entries and terms.

The tab is organized into collapsible sections:

* **Search Appearance** — Meta title, description, and a live search preview.
* **Social Appearance** — OG title, description, image, social images generator settings, and a live social preview.
* **Indexing** — Noindex and nofollow toggles.
* **Canonical URL** — Canonical URL type and target.
* **Sitemap** — Sitemap inclusion, priority, and change frequency.
* **Structured Data** — Custom JSON-LD.

{% hint style="info" %}
The SEO tab can be hidden from non-super-users using the **Editable** toggle in the [collection/taxonomy configuration](settings-and-defaults.md#collection--taxonomy-configuration). This is useful when you want SEO fields to be computed entirely from defaults.
{% endhint %}

## Token Input

Titles and descriptions use a token-based input field. Tokens are dynamic placeholders that resolve to field values at render time.

### Using Tokens

Type `{{` in any title or description field to open the token autocomplete. Select a field from the dropdown to insert it as a token. Tokens use Antlers syntax and support modifiers:

```
{{ title }}
{{ title | upper }}
{{ content | truncate(90, '...') }}
```

The token input shows a character count and enforces recommended limits:

| Field | Character Limit |
| ----- | --------------- |
| Meta Title | 60 |
| Meta Description | 160 |
| OG Title | 70 |
| OG Description | 200 |

Available tokens are derived from the entry or term's blueprint fields. You can also register [custom tokens](../extending/tokens.md) for additional fieldtypes or custom values.

## Search & Social Previews

The SEO tab includes inline previews that update in real time as you edit:

* **Search Preview** — Shows how the entry or term will appear in Google search results, including the title, description, URL, and breadcrumbs.
* **Social Preview** — Shows how the entry or term will appear when shared on social media. You can switch between Open Graph, Twitter Summary, and Twitter Summary Large Image presets.

## AI Content Generation

Advanced SEO can generate titles and descriptions using AI. This requires the [Laravel AI SDK](https://github.com/laravel/ai) to be installed and configured.

### Setup

{% stepper %}
{% step %}
### Install Laravel AI

```shell
composer require laravel/ai
```

Configure your AI provider in `config/ai.php`. Any provider supported by the Laravel AI SDK works (OpenAI, Anthropic, Google, etc.).
{% endstep %}

{% step %}
### Enable AI in Advanced SEO

```php
// config/advanced-seo.php
'ai' => [
    'enabled' => true,
    'provider' => null, // Uses the default provider from config/ai.php
    'model' => null,    // Uses the provider's cheapest model
],
```
{% endstep %}
{% endstepper %}

### Usage

When AI is enabled, a generate action appears in the token input suggestions. AI can generate:

* Meta titles
* Meta descriptions
* OG titles
* OG descriptions

The AI analyzes the entry or term's content to generate contextual text. It respects the site's language for multilingual generation and enforces the same character limits as the token input.

{% hint style="info" %}
The entry or term needs at least 50 characters of content for AI generation to work.
{% endhint %}

## Data Inheritance

SEO field values follow a cascading inheritance model:

1. **Entry/Term values** — If a field has a custom value, it is used.
2. **Collection/Taxonomy defaults** — If no custom value is set, the default from the collection or taxonomy is used.
3. **Site defaults** — If no collection/taxonomy default is set, the site default is used.

Fields that are left empty in the token input inherit their value from the next level in the cascade. You can reset a customized field back to its inherited value using the **Reset** action.

For multi-site setups, the [origins](settings-and-defaults.md#origins) system adds another layer of inheritance between sites.
