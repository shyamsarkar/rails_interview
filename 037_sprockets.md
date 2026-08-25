## What is Sprockets?

**Sprockets** is a Ruby library used by Rails to manage and serve static assets such as:

- JavaScript
- CSS and Sass
- Images
- Fonts

It provides an asset pipeline that finds source files, processes them, combines them when configured, and generates browser-ready files.

## Why is Sprockets Used?

- Organizes assets into a predictable directory structure
- Combines multiple CSS or JavaScript files
- Compresses assets to reduce their size
- Adds fingerprints to filenames for cache busting
- Precompiles assets before deployment

The result is faster asset delivery and safer browser caching.

## How Does the Sprockets Pipeline Work?

1. Rails looks for source assets in directories such as `app/assets`, `lib/assets`, and `vendor/assets`.
2. A manifest declares which files should be included in the application bundles.
3. Sprockets resolves dependencies and processes the files.
4. The resulting assets are compressed and fingerprinted.
5. Rails serves the generated files, often from the `public/assets` directory in production.

For example, several source files can become one browser asset:

```text
app/assets/stylesheets/reset.css
app/assets/stylesheets/application.css
app/assets/javascripts/users.js
app/assets/javascripts/application.js

public/assets/application-8f31c2.css
public/assets/application-a91d44.js
```

## Asset Manifests

A manifest is the entry point for an asset bundle. In older Rails applications, directives were commonly written like this:

```js
// app/assets/javascripts/application.js
//= require rails-ujs
//= require_tree .
```

```css
/* app/assets/stylesheets/application.css */
/*= require_self */
/*= require_tree . */
```

- `require` includes a specific asset.
- `require_tree` includes assets from a directory and its subdirectories.
- `require_self` includes the contents of the manifest itself.

Modern applications may use `link` directives or JavaScript package imports instead, depending on their Rails setup.

## Fingerprinting

Sprockets adds a digest to the filename based on the asset contents:

```text
application.css
application-8f31c2.css
```

When the contents change, the digest changes too. This lets browsers cache assets for a long time while ensuring that a new version is downloaded after a deployment. Rails helpers such as `stylesheet_link_tag` and `javascript_include_tag` automatically use the fingerprinted name.

## Precompilation

In production, assets are normally compiled before the application is started:

```bash
RAILS_ENV=production rails assets:precompile
```

This generates the final CSS, JavaScript, and other assets. The generated files can be served by Rails or by a web server such as Nginx or a CDN.

## Sprockets vs. Modern Rails Asset Tools

Sprockets is the traditional Rails asset pipeline. It is still useful for CSS, images, and applications that use manifest directives. Newer Rails applications may use alternatives such as:

- **Propshaft** for a simpler asset pipeline
- **Import maps** for JavaScript without a bundler
- **jsbundling-rails** for JavaScript bundlers such as esbuild or Bun
- **cssbundling-rails** for CSS build tools

These tools can coexist with Sprockets, but they solve different parts of the asset workflow.

## Interview Summary

Sprockets is the Rails asset pipeline library. It manages asset dependencies, processes and compresses source files, fingerprints filenames for cache busting, and precompiles assets for production. It is primarily responsible for serving static assets efficiently, not for writing application JavaScript or CSS.