---
description: Register custom token normalizers for additional fieldtypes or add custom value tokens to the token input.
---

# Tokens

{% hint style="info" %}
Custom tokens require the [Pro edition](../getting-started/editions.md).
{% endhint %}

The [token input](../usage/on-page-seo.md#token-input) can be extended in two ways:

* **Token Normalizers** — Convert field values into strings so they can be used as tokens.
* **Value Tokens** — Tokens that provide static or dynamic values.

## Token Normalizers

Token normalizers convert field values into strings so they can be used as tokens. Advanced SEO includes normalizers for common fieldtypes like Text, Bard, and Markdown. If you need support for a fieldtype that isn't covered, create a normalizer for it.

A normalizer requires both a PHP class for server-side rendering and a JavaScript normalizer for the live preview in the Control Panel.

### Creating a Normalizer

Extend the `TokenNormalizer` class and implement the `fieldtype()` and `normalize()` methods:

```php
namespace App\Seo\Tokens;

use Aerni\AdvancedSeo\Tokens\TokenNormalizer;
use Statamic\Fields\Value;

class TagsTokenNormalizer extends TokenNormalizer
{
    public function fieldtype(): string
    {
        return 'tags';
    }

    public function normalize(Value $value): ?string
    {
        return collect($value->value())->join(', ');
    }
}
```

Register a JavaScript normalizer for the same fieldtype handle using `Statamic.$advancedSeo.normalizers.add()`. The normalizer receives the raw field value and field meta:

```js
Statamic.booting(() => {
    Statamic.$advancedSeo.normalizers.add('tags', (value, meta) => {
        return (value ?? []).join(', ');
    });
});
```

### API

| Method | Description |
| --- | --- |
| `fieldtype(): string` | Return the handle of the fieldtype this normalizer is for |
| `normalize(Value $value): ?string` | Convert the field value to a plain string so it can be used as a token |

<details>
<summary>Built-in normalizers for reference</summary>

| Normalizer | Fieldtype | Behavior |
| --- | --- | --- |
| `TextTokenNormalizer` | `text` | Strips tags and trims |
| `TextareaTokenNormalizer` | `textarea` | Strips tags and trims |
| `BardTokenNormalizer` | `bard` | Converts Bard content to plain text |
| `MarkdownTokenNormalizer` | `markdown` | Converts Markdown to plain text |
| `UsersTokenNormalizer` | `users` | Resolves user names (comma-separated with "and") |

</details>

## Value Tokens

Value tokens provide static or dynamic values that appear alongside field tokens in the autocomplete. They are useful for values that don't come from a blueprint field.

### Creating a Value Token

Extend the `ValueToken` class and implement the `handle()` and `value()` methods. The handle is used as the Antlers tag name. A handle of `company_name` becomes `{{ company_name }}` in the token input:

```php
namespace App\Seo\Tokens;

use Aerni\AdvancedSeo\Tokens\ValueToken;

class CompanyNameToken extends ValueToken
{
    public function handle(): string
    {
        return 'company_name';
    }

    public function value(): ?string
    {
        return config('app.company_name');
    }
}
```

The `$this->parent` property gives you access to the entry or term the token is being resolved for, enabling context-aware values.

### API

| Method | Description |
| --- | --- |
| `handle(): string` | Return the token handle, used as the Antlers tag name |
| `value(): ?string` | Return the token's value |
| `display(): string` | Return the display label shown in the autocomplete. Defaults to a title-cased version of the handle |
| `group(): string` | Return the group name for the autocomplete dropdown. Defaults to "Common" |

<details>
<summary>Built-in value tokens for reference</summary>

| Token | Handle | Value |
| --- | --- | --- |
| `SeparatorToken` | `separator` | The configured title separator |
| `SiteNameToken` | `site_name` | The configured site name |

</details>

## Registration

Both token normalizers and value tokens can be registered in two ways:

### Via Config

Add the class to the `tokens` array in `config/advanced-seo.php`:

```php
'tokens' => [
    App\Seo\Tokens\TagsTokenNormalizer::class,
    App\Seo\Tokens\CompanyNameToken::class,
],
```

### Via Service Provider

Register the class in a service provider's `boot` method:

```php
public function boot(): void
{
    \App\Seo\Tokens\TagsTokenNormalizer::register();
    \App\Seo\Tokens\CompanyNameToken::register();
}
```
