# Installation

## Install Addon

Install the addon with Composer:

```shell
composer require aerni/advanced-seo
```

The installation process will automatically publish the addon's config to `config/advanced-seo.php`. Make sure to check it out, as there are lots of configuration options to tailor the features to your needs.

## Setup Views

Add the head tag somewhere in your layout's `<head>`:

```html
<head>

    <!-- Antlers -->
    {{ seo:head }}
    
    <!-- Blade -->
    @seo('head')

</head>
```

Add the body tag right after the opening `<body>` in your layout. This tag is only needed when using Google Tag Manager.

```html
<body>

    <!-- Antlers -->
    {{ seo:body }}
    
    <!-- Blade -->
    @seo('body')

</body>
```
