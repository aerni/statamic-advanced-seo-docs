---
description: Generate SEO titles and descriptions with AI, with site-wide and per-collection instructions.
---

# AI Copywriting

{% hint style="info" %}
AI content generation requires the [Pro edition](../getting-started/editions.md).
{% endhint %}

Advanced SEO can generate meta titles, meta descriptions, OG titles, and OG descriptions using AI. Generation is powered by the [Laravel AI SDK](https://github.com/laravel/ai), so any provider it supports (OpenAI, Anthropic, Google, etc.) works.

## Setup

Run the install command and select **AI Copywriting**:

```shell
php please seo:install
```

This installs the Laravel AI SDK and enables AI in `config/advanced-seo.php`. After installation, configure your provider in `config/ai.php`.

If you don't want Advanced SEO to use the default provider from `config/ai.php`, set a specific provider and model in `config/advanced-seo.php`:

```php
'ai' => [
    'enabled' => true,
    'provider' => 'anthropic',
    'model' => 'claude-haiku-4-5-20251001',
],
```

## Enabling AI per Collection or Taxonomy

Once AI is enabled in the config, you can control it on a per-collection and per-taxonomy basis. Click the **Configure** button on any collection or taxonomy in the SEO section and toggle **Enable AI** on or off.

When the toggle is off, the generate action is hidden from the entries and terms of that collection or taxonomy. This is useful when AI generation only makes sense for some content types (like blog posts or products) but not others (like legal pages).

## Usage

When AI is enabled, a generate action appears in the meta title, meta description, OG title, and OG description fields on entries and terms. Click it to generate contextual text based on the entry or term's content. The site's language is respected for multilingual generation.

{% hint style="info" %}
The entry or term needs at least 50 characters of content for AI generation to work.
{% endhint %}

## Instructions

You can guide how the AI writes by providing custom instructions. Instructions are configured in two places, and both are combined when generating content:

* **Site defaults** — Global instructions that apply to all AI generation. Set these in the **AI** section of your [site defaults](settings-and-defaults.md#site-defaults). Use this for general tone, voice, and vocabulary rules.
* **Collection & Taxonomy configuration** — Instructions specific to one collection or taxonomy. Set these in the **Configure** screen alongside the AI toggle. Use this for content-type-specific context.

Both levels are combined when generating content, so the AI sees your global tone rules together with the scoped context for that collection or taxonomy.
