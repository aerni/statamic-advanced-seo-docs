---
description: Automatically generate social images for your entries and taxonomy terms using customizable themes.
---

# Social Images Generator

## Setup

Run the install command and select **Social Images Generator**:

```shell
php please seo:install
```

This installs the required dependencies, enables the feature in your config, and creates a default theme.

## Requirements

The generator uses [spatie/laravel-screenshot](https://spatie.be/docs/laravel-screenshot/v1/introduction) under the hood, which supports two screenshot drivers:

* **Browsershot** (default) - Requires [Puppeteer](https://github.com/puppeteer/puppeteer) installed on your server.
* **Cloudflare Browser Rendering** — A cloud-based alternative that doesn't require Puppeteer.

The install command lets you choose your driver and handles the setup. To configure the screenshot driver manually, publish the config:

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

The install command enables this automatically. To enable it manually, set the following in your config:

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

Images are automatically generated when you save an entry or term. Smart regeneration ensures images are only regenerated when content actually changes. Saving without content changes won't trigger regeneration.

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

The generator produces a single Open Graph image that is shared between Open Graph and X (Twitter) meta tags. The **Twitter Card** setting on each [collection/taxonomy configuration](settings-and-defaults.md#configuration) determines which preset is used to resize the shared image for the Twitter meta tags.

## Themes

Themes are Antlers templates that define the visual design of your generated social images. You can create multiple themes and switch between them in the publish form.

### Creating a Theme

Run the following command to create a new theme:

```shell
php please seo:theme {name}
```

This publishes a default layout and an `open_graph.antlers.html` template:

```
resources/views/social_images/
├── layout.antlers.html
└── {name}/
    └── open_graph.antlers.html
```

### Template Structure

Each theme needs an `open_graph.antlers.html` template. Design your template like any Statamic view, using the full power of Antlers: variables, tags, partials, and any styling approach you prefer.

The template receives all of the entry or term's augmented data as variables.

### Previewing

You can preview your templates in the browser using this URL pattern:

```
https://site.test/!/advanced-seo/social-images/{theme}/open-graph/{id}/{site}
```

| Variable | Description | Example |
| -------- | ----------- | ------- |
| `theme` | The theme handle | `default` |
| `id` | The entry or term ID | `4358df35-c7fe-4774-97ad-02af0e2dea3b` |
| `site` | The site handle | `default` |

### Theme Restrictions

You can restrict which themes are available for a specific collection or taxonomy in its [configuration](settings-and-defaults.md#configuration). When themes are restricted, only the selected themes appear in the theme dropdown on entries and terms.

### Inline Preview

The social preview in the publish form renders the template used to generate the social image. It updates in real time as you edit the entry or term. When multiple themes are available, you can switch between them to see how each looks before saving.

