---
description: Configure storage drivers, crawling environments, analytics environments, view composer, and other options in config/advanced-seo.php.
---

# Configuration

Advanced SEO is configured through `config/advanced-seo.php`. The config file is automatically published during [installation](../getting-started/installation.md). To publish it manually:

```shell
php artisan vendor:publish --tag=advanced-seo-config
```

{% hint style="info" %}
When a feature is disabled in the config, all its associated fields are removed from the blueprints.
{% endhint %}

## Storage Driver

Advanced SEO supports two storage drivers: `file` and `eloquent`. The File driver stores data as flat-files alongside your content, while the Eloquent driver stores data in a database using the [Statamic Eloquent Driver](https://github.com/statamic/eloquent-driver).

### File Driver

The File driver is the default. Data is saved in `content/seo`, but you can customize the directory:

```php
'driver' => 'file',
'directory' => base_path('content/seo'),
```

To switch back to flat-files from Eloquent, run the following command. It will update the config and give you the option to export existing data from the database to flat-files:

```shell
php please seo:switch-to-file
```

### Eloquent Driver

{% hint style="info" %}
The Eloquent driver requires the [Pro edition](../getting-started/editions.md).
{% endhint %}

To use the Eloquent driver, first install the [Statamic Eloquent Driver](https://github.com/statamic/eloquent-driver/tree/master):

```shell
composer require statamic/eloquent-driver
```

Then run the following command. It will set the driver in your config to `eloquent`, publish and run the database migrations, and give you the option to import existing data from flat-files into the database:

```shell
php please seo:switch-to-eloquent
```

{% hint style="info" %}
Neither command will delete any existing data from the previous driver. You may clean up old flat-files or database tables yourself once you're satisfied with the migration.
{% endhint %}

## GraphQL

Enable the GraphQL API. See [GraphQL](../reference/graphql.md) for available queries and types.

```php
'graphql' => false,
```

{% hint style="info" %}
Statamic's GraphQL API must also be enabled. Refer to the [Statamic documentation](https://statamic.dev/graphql) for setup.
{% endhint %}

## View Composer

Advanced SEO uses a view composer to inject the `seo` variable into your Antlers and Blade views. This is what makes values like `{{ seo:title }}` available in your templates. By default, the view composer is applied to all views (`'*'`). If you want to limit which views receive SEO data:

```php
'view_composer' => [
    'layouts.app',
    'pages.*',
],
```

## Social Images

### Asset Container

Configure the asset container for social images. Generated images are saved in a `social_images` directory within the configured container:

```php
'social_images' => [
    'container' => 'assets',
],
```

### Presets

Customize the dimensions of social images:

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

### Generator

The generator automatically creates social images from customizable Antlers templates. See [Social Images Generator](social-images-generator.md) for full setup instructions.

```php
'social_images' => [
    'generator' => [
        'enabled' => false,
        'queue' => 'default',
    ],
],
```

## Favicons

The favicons feature renders favicon meta tags from the SVG uploaded in [site defaults](settings-and-defaults.md#site-defaults). You can disable it if you manage favicons yourself:

```php
'favicons' => [
    'enabled' => true,
    'container' => 'assets',
],
```

## Crawling

The crawling config controls which environments allow search engine crawling. By default, crawling is only enabled for the `production` environment:

```php
'crawling' => [
    'environments' => ['production'],
],
```

In all other environments:

* The robots meta tag is set to `noindex, nofollow`
* Crawling-related meta tags like `canonical` and `alternate` are removed
* Sitemap routes are disabled

## Sitemaps

Domain-scoped XML sitemaps are generated automatically. See [Sitemaps](sitemaps.md) for details.

```php
'sitemap' => [
    'enabled' => true,
    'path' => storage_path('statamic/sitemaps'),
    'queue' => 'default',
    'custom' => [
        //
    ],
],
```

## Analytics

Analytics scripts only render in the configured environments. Defaults to `production`:

```php
'analytics' => [
    'environments' => ['production'],
],
```

You can also disable individual trackers to remove their section from the [site defaults](settings-and-defaults.md#site-defaults):

```php
'analytics' => [
    'fathom' => true,
    'cloudflare_analytics' => true,
    'google_tag_manager' => true,
],
```

## Site Verification

Site verification adds Google and Bing verification code fields to the [site defaults](settings-and-defaults.md#site-defaults). Disable it to remove those fields:

```php
'site_verification' => true,
```

## Tokens

Register custom token normalizers and value tokens. See [Tokens](../extending/tokens.md) for details.

```php
'tokens' => [
    //
],
```

## AI

See [AI Content Generation](on-page-seo.md#ai-content-generation) for setup and configuration.

```php
'ai' => [
    'enabled' => false,
    'provider' => null,
    'model' => null,
],
```
