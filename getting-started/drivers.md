---
description: Advanced SEO supports saving data in either flat-files or in a database.
---

# Drivers

## File

The `File` driver is the default database driver. If you've previously switched to using the database, you may switch back to using flat-files using this command:

```
php please seo:switch-to-file
```

You may also uninstall the Statamic Eloquent Driver if you don't need it for any other content:

```
composer remove statamic/eloquent-driver
```

## Database

The process of switching from flat-files to a database is painless. All you need to do is run a few commands.

First, install the [Statamic Eloquent Driver](https://github.com/statamic/eloquent-driver/tree/master):

```
composer require statamic/eloquent-driver
```

Next, run the following command. It will update Advanced SEO's config file to use the Eloquent driver and copy and migrate the relevant database migrations. It will also give you the option to import existing data from flat-files into the database.

```
php please seo:switch-to-eloquent
```

{% hint style="info" %}
The above command will **not** delete any Advanced SEO flat-files. You may do this yourself once you're satisfied with the migration.
{% endhint %}
