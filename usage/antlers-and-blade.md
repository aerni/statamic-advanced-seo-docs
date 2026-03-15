# Antlers & Blade

## Accessing Variables

All the SEO values are processed and added to your views in an array with key `seo`. This array includes all the SEO data of the current entry or term, as well as global site defaults and computed values like `hreflang`.

You may access any of those values like you would with any other value in Antlers or Blade:

```html
<!-- Antlers -->
{{ seo:title }}

<!-- Blade -->
{{ $seo->title }}
```

## Antlers Tags & Blade Directives

There are a couple of tags and directives to use in your views:

| Antlers Tag      | Blade Directive | Description                               |
| ---------------- | --------------- | ----------------------------------------- |
| `{{ seo:head }}` | `@seo('head')`  | Render the head view                      |
| `{{ seo:body }}` | `@seo('body')`  | Render the body view                      |
| `{{ seo:dump }}` | `@seo('dump')`  | Dump all the SEO data of the current page |
