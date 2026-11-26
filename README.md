# Symfony Flex recipes — alexandrebulete/*

Recipes for the `alexandrebulete/ddd-*` packages: Flex registers each bundle and
copies the configuration it needs when the package is required. No install
script in any bundle, nothing to wire by hand.

| Package | Recipe | What it does |
|---|---|---|
| ddd-symfony-bundle | 1.0 | registers the bundle |
| ddd-doctrine-bundle | 1.0 | registers the bundle |
| ddd-apiplatform-bundle | 1.0 | registers the bundle |
| ddd-sylius-bundle | 1.0 | registers the bundle |
| ddd-iam-bundle | 2.0 | registers the bundle, `config/packages/iam.yaml`, `config/routes/iam.yaml` |
| ddd-outbox-bundle | 1.0 | registers the bundle, `config/routes/outbox.yaml` |
| ddd-activity-bundle | 1.0 | registers the bundle |
| ddd-notification-bundle | 1.0 | registers the bundle |
| ddd-scheduler-bundle | 1.0 | registers the bundle |
| ddd-seo-bundle | 1.0 | registers the bundle, `config/routes/seo.yaml` (sitemap.xml, robots.txt) |

## Scope

Recipes of the packages published under `alexandrebulete/*`, and nothing else:

- one recipe per package that ships a bundle, in the package's own folder;
  libraries without a bundle (ddd-foundation, the bridges) need none;
- never a recipe for a third-party package: it would shadow the official one
  for every application using this endpoint — that goes to
  symfony/recipes-contrib;
- never project-specific configuration: that lives in the project.

## Using it in an application

```bash
composer config --json extra.symfony.endpoint \
  '["https://raw.githubusercontent.com/AlexandreBulete/symfony-recipes/flex/main/index.json", "flex://defaults"]'
```

The order matters: the first endpoint that knows a package wins, so this one
comes before `flex://defaults`. Already installed packages get their recipe
with `composer recipes:install alexandrebulete/<package> --force`.

## What a recipe does not do

A recipe only creates files that do not exist yet. Files owned by another
package's recipe (`security.yaml`, `messenger.yaml`, `doctrine.yaml`) stay the
application's job — the bundles document what to put there.

## Layout

The symfony/recipes layout: `<vendor>/<package>/<version>/manifest.json`, the
files to copy next to it, and an optional `post-install.txt`. The version is the
first package version the recipe applies to; Flex picks the highest one not
above the installed package.

`bin/build <dir> <base url>` compiles them into a Flex endpoint (`index.json`
and one JSON per recipe) and rejects unknown manifest keys or a
`copy-from-recipe` that matches no file. On every push to `main`, the workflow
publishes the result on the `flex/main` branch.

```bash
docker run --rm -v "$PWD":/app -w /app php:8.4-cli php bin/build /tmp/out http://localhost
```
