---
description: >-
  Create custom social image themes to control the look of your
  generated social images.
---

# Social Image Themes

Themes are Antlers templates that define the visual design of your generated social images. You can create multiple themes and restrict which ones are available per collection or taxonomy.

## Creating a Theme

Run the following command to create a new theme:

```shell
php please seo:theme {name}
```

This publishes a default layout and an `open_graph.antlers.html` template to:

```
resources/views/social_images/{name}/
├── layout.antlers.html
└── open_graph.antlers.html
```

## Template Structure

Each theme needs an `open_graph.antlers.html` template. This is the only template used for generation — both Open Graph and X (Twitter) share the same generated image.

Design your template like any Statamic view, using the full power of Antlers — variables, tags, partials, and any styling approach you prefer.

The template receives all of the entry or term's augmented data as variables.

## Previewing

You can preview your templates in the browser using this URL pattern:

```
https://site.test/!/advanced-seo/social-images/{theme}/open-graph/{id}/{site}
```

| Variable | Description | Example |
| -------- | ----------- | ------- |
| `theme` | The theme handle | `default` |
| `id` | The entry or term ID | `4358df35-c7fe-4774-97ad-02af0e2dea3b` |
| `site` | The site handle | `default` |

## Theme Restrictions

You can restrict which themes are available for a specific collection or taxonomy in its [configuration](../usage/settings-and-defaults.md#collection--taxonomy-configuration). When themes are restricted, only the selected themes appear in the theme dropdown on entries and terms.
