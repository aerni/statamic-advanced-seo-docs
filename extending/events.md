---
description: Listen to Advanced SEO events to react to changes in SEO configuration and content data.
---

# Events

Advanced SEO dispatches events when SEO data is saved or deleted. All events are in the `Aerni\AdvancedSeo\Events` namespace.

## Available Events

### SeoSetConfigSaved

`Aerni\AdvancedSeo\Events\SeoSetConfigSaved`

Dispatched after a set's configuration has been saved.

```php
public function handle(SeoSetConfigSaved $event)
{
    $event->config;
}
```

### SeoSetConfigDeleted

`Aerni\AdvancedSeo\Events\SeoSetConfigDeleted`

Dispatched after a set's configuration has been deleted.

```php
public function handle(SeoSetConfigDeleted $event)
{
    $event->config;
}
```

### SeoSetLocalizationSaved

`Aerni\AdvancedSeo\Events\SeoSetLocalizationSaved`

Dispatched after a localization has been saved.

```php
public function handle(SeoSetLocalizationSaved $event)
{
    $event->localization;
}
```

### SeoSetLocalizationDeleted

`Aerni\AdvancedSeo\Events\SeoSetLocalizationDeleted`

Dispatched after a localization has been deleted.

```php
public function handle(SeoSetLocalizationDeleted $event)
{
    $event->localization;
}
```
