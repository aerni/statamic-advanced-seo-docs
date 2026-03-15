---
description: >-
  Register custom token normalizers for additional fieldtypes or add custom
  value tokens to the token input.
---

# Custom Tokens

The [token input](../usage/on-page-seo.md#token-input) supports two types of custom tokens:

* **Token Normalizers** — Convert field values from custom fieldtypes into strings so they can be used as tokens.
* **Value Tokens** — Provide custom dynamic values (like the built-in `separator` and `site_name` tokens).

Both are registered via the `tokens` config key in `config/advanced-seo.php`:

```php
'tokens' => [
    App\Seo\Tokens\MyFieldNormalizer::class,
    App\Seo\Tokens\MyValueToken::class,
],
```

## Token Normalizers

Token normalizers convert field values into strings for display in SEO tags. Advanced SEO includes normalizers for common fieldtypes (Text, Textarea, Bard, Markdown, Users). If you use a custom fieldtype that isn't covered, create a normalizer for it.

### Creating a Normalizer

Extend the `TokenNormalizer` class and implement two methods:

```php
namespace App\Seo\Tokens;

use Aerni\AdvancedSeo\Tokens\TokenNormalizer;
use Statamic\Fields\Value;

class MyFieldNormalizer extends TokenNormalizer
{
    public function fieldtype(): string
    {
        return 'my_fieldtype'; // The fieldtype handle
    }

    public function normalize(Value $value): ?string
    {
        return trim(strip_tags($value->value() ?? ''));
    }
}
```

<details>
<summary>Built-in normalizers for reference</summary>

| Normalizer | Fieldtype | Behavior |
| --- | --- | --- |
| `TextTokenNormalizer` | `text` | Strips tags and trims |
| `TextareaTokenNormalizer` | `textarea` | Strips tags and trims |
| `BardTokenNormalizer` | `bard` | Converts Bard content to plain text |
| `MarkdownTokenNormalizer` | `markdown` | Converts Markdown to plain text |
| `UsersTokenNormalizer` | `users` | Resolves user names |

</details>

## Value Tokens

Value tokens provide custom dynamic values that appear alongside field tokens in the autocomplete. They are useful for values that don't come from a blueprint field.

### Creating a Value Token

Extend the `ValueToken` class and implement the `handle()` and `value()` methods:

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

<details>
<summary>Built-in value tokens for reference</summary>

| Token | Handle | Value |
| --- | --- | --- |
| `SeparatorToken` | `separator` | The configured title separator |
| `SiteNameToken` | `site_name` | The configured site name |

</details>
