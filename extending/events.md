---
description: >-
  Listen to Advanced SEO events to react to changes in SEO configuration
  and content data.
---

# Events

Advanced SEO dispatches events when SEO data is saved or deleted. All events are in the `Aerni\AdvancedSeo\Events` namespace.

## Available Events

### SeoSetConfigSaved

Fired when a set's configuration is saved (enabled state, origins, sitemap toggle, etc.).

```php
use Aerni\AdvancedSeo\Events\SeoSetConfigSaved;

Event::listen(SeoSetConfigSaved::class, function (SeoSetConfigSaved $event) {
    $config = $event->config; // SeoSetConfig instance
});
```

### SeoSetConfigDeleted

Fired when a set's configuration is deleted.

```php
use Aerni\AdvancedSeo\Events\SeoSetConfigDeleted;
```

### SeoSetLocalizationSaved

Fired when a localization's data is saved (default field values for a specific site).

```php
use Aerni\AdvancedSeo\Events\SeoSetLocalizationSaved;

Event::listen(SeoSetLocalizationSaved::class, function (SeoSetLocalizationSaved $event) {
    $localization = $event->localization; // SeoSetLocalization instance
});
```

### SeoSetLocalizationDeleted

Fired when a localization is deleted.

```php
use Aerni\AdvancedSeo\Events\SeoSetLocalizationDeleted;
```

## Git Integration

All events implement the `ProvidesCommitMessage` interface, which means they automatically integrate with Statamic's [Git integration](https://statamic.dev/git-automation). When Git automation is enabled, saving or deleting SEO data will trigger a Git commit.
