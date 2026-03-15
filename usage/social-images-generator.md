---
description: >-
  Automatically generate social images for your entries and taxonomy terms
  using customizable themes.
---

# Social Images Generator

## Requirements

The generator uses [spatie/laravel-screenshot](https://spatie.be/docs/laravel-screenshot/v1/introduction) under the hood, which supports two screenshot drivers:

* **Browsershot** (default) — Requires [Puppeteer](https://github.com/puppeteer/puppeteer) installed on your server.
* **Cloudflare Browser Rendering** — A cloud-based alternative that doesn't require Puppeteer.

To configure the screenshot driver, publish the config:

```shell
php artisan vendor:publish --tag=laravel-screenshot-config
```

{% tabs %}
{% tab title="Browsershot (default)" %}
Browsershot requires [Puppeteer](https://github.com/puppeteer/puppeteer) installed on your server. Refer to the [Browsershot documentation](https://spatie.be/docs/browsershot/v5/requirements) for setup instructions.
{% endtab %}

{% tab title="Cloudflare Browser Rendering" %}
Cloudflare Browser Rendering is a cloud-based alternative that doesn't require Puppeteer. Configure the driver in your published `config/screenshot.php`:

```php
'driver' => 'cloudflare',

'cloudflare' => [
    'account_id' => env('CLOUDFLARE_ACCOUNT_ID'),
    'api_token' => env('CLOUDFLARE_API_TOKEN'),
],
```

Refer to the [spatie/laravel-screenshot documentation](https://spatie.be/docs/laravel-screenshot/v1/introduction) for more details.
{% endtab %}
{% endtabs %}

## Enable Generator

{% stepper %}
{% step %}
### Enable globally

Enable the social images generator in your config:

```php
// config/advanced-seo.php
'social_images' => [
    'generator' => [
        'enabled' => true,
    ],
],
```
{% endstep %}

{% step %}
### Enable per collection/taxonomy

Head to the SEO section in the Control Panel. Click the **Configure** button on the collection or taxonomy you want to enable the generator for, and toggle **Social Images Generator** on.
{% endstep %}
{% endstepper %}

{% hint style="info" %}
The generator supports both entries and taxonomy terms.
{% endhint %}

## Generating Images

### On Save

Images are automatically generated when you save an entry or term. Smart regeneration ensures images are only regenerated when content actually changes — saving without content changes won't trigger regeneration.

Generation runs asynchronously after the response is sent. For better performance, configure a queue:

```php
'social_images' => [
    'generator' => [
        'queue' => 'default',
    ],
],
```

### On Demand

If a generated image is missing (e.g. accidentally deleted), it will be regenerated on-the-fly on the next frontend request.

### Action

You can generate images at any time using the action in the collection or taxonomy listing view.

### Command

Generate social images for all entries and terms at once:

```shell
php please seo:generate-images
```

## Unified Social Image

The generator produces a single Open Graph image that is shared between Open Graph and X (Twitter) meta tags. The **Twitter Card** setting on each [collection/taxonomy configuration](settings-and-defaults.md#collection--taxonomy-configuration) determines which preset is used to resize the shared image for the Twitter meta tags.

## Themes

Themes are customizable Antlers templates that define the look of your social images. You can have multiple themes and switch between them in the publish form.

To create a theme:

```shell
php please seo:theme {name}
```

This publishes a default layout and an `open_graph.antlers.html` template to `resources/views/social_images/{name}/`.

For details on creating and customizing themes, see [Social Image Themes](../extending/social-image-themes.md).

### Theme Restrictions

You can restrict which themes are available for a specific collection or taxonomy in its [configuration](settings-and-defaults.md#collection--taxonomy-configuration).

### Inline Preview

The publish form includes an inline preview of the generated social image. When multiple themes are available, you can switch between them to see how each looks before saving.

## Configuration

### Asset Container

Configure the asset container for your social images. Images are saved in a `social_images` directory within the configured container:

```php
'social_images' => [
    'container' => 'assets',
],
```

### Presets

Customize the dimensions of your social images:

```php
'social_images' => [
    'presets' => [
        'open_graph' => ['width' => 1200, 'height' => 630],
        'twitter_summary' => ['width' => 240, 'height' => 240],
        'twitter_summary_large_image' => ['width' => 1200, 'height' => 630],
    ],
],
```

The `open_graph` preset defines the generated image size. The `twitter_summary` and `twitter_summary_large_image` presets define the dimensions used to resize the shared image for the Twitter meta tags via Glide.
