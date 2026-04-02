---
description: Install Advanced SEO in your Statamic project using Composer.
---

# Installation

Install the addon with Composer:

```shell
composer require aerni/advanced-seo
```

Then run the install command to complete the setup:

```shell
php artisan seo:install
```

The command will publish the config, add the required SEO tags to your layout, and ask you about optional features like social images generation, and AI content generation. If you're migrating from SEO Pro or Aardvark SEO, the installer will also offer to migrate your existing content.

## Migration

Switching from SEO Pro or Aardvark SEO? Advanced SEO can automatically migrate your existing data. The install command offers this during setup, but you can also run it separately at any time:

```shell
php please seo:migrate
```
