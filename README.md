# devplayer55221.github.io

Personal portfolio and blog built with [Hugo](https://gohugo.io/).

The site uses `hugo-paper` as the base theme, but the visible design is customized through project-level Hugo overrides. Blog posts and pages remain regular Markdown files under `content/`.

## Theme Overrides

The upstream theme lives in:

```text
themes/hugo-paper/
```

Customizations are kept outside the theme directory so the base theme can remain intact.

Project-level overrides:

```text
layouts/_default/baseof.html
layouts/_default/list.html
layouts/_default/single.html
layouts/partials/head.html
layouts/partials/header.html
layouts/partials/footer.html
assets/custom.css
```

These files replace or customize the matching templates from `hugo-paper`.

## Changes

- Replaced the default `hugo-paper` visual style with a custom minimal portfolio theme.
- Added a custom header, navigation layout, footer, homepage post list, and article page layout.
- Kept the homepage behavior of listing blog posts.
- Removed the default browser favicon by using a blank data favicon.
- Removed the custom brand mark before the site title.
- Added a small live local-time footer detail.
- Loaded only `assets/custom.css` for the custom theme styles.
- Preserved dark-mode behavior, social links, pagination, tags, post navigation, Mermaid support, and optional comments integrations.
- The upstream `themes/hugo-paper/` files were not modified for the custom theme.

## Writing Posts

Add posts as Markdown files under:

```text
content/post/
```

You can also create a new post with:

```bash
hugo new post/my-new-post.md
```

## Local Preview

Run the Hugo development server:

```bash
hugo server --disableFastRender
```

Then open:

```text
http://localhost:1313/
```

## Production Build

Build the static site with:

```bash
hugo --gc --minify
```

Generated output is written to:

```text
public/
```

