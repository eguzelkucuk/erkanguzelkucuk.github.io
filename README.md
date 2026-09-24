# eguzelkucuk.github.io

Personal site and blog — <https://eguzelkucuk.github.io>

Built with [Jekyll](https://jekyllrb.com) and the [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy)
theme, built and deployed by GitHub Actions to GitHub Pages.

## Writing a post

Create a file in `_posts/` named `YYYY-MM-DD-slug.md`:

```markdown
---
title: "Post title"
date: 2026-09-24 10:00:00 +0300
categories: [Category, Subcategory]
tags: [en, dotnet]
description: One-line summary used in listings and SEO.
---

Content here.
```

Rules that bite:

- The filename date format is mandatory — Jekyll ignores files that don't match.
- Future-dated posts are not published until that date passes.
- `tags` must include the language: `en` or `tr`. At most two `categories`.
- `pin: true` keeps a post at the top of the home page.

Then push — Actions builds and deploys:

```bash
git add _posts/2026-09-24-my-post.md && git commit -m "post: my post" && git push
```

## Local preview (optional)

The build runs fine on Actions without any local setup. To preview locally you need Ruby 3.x
(macOS ships 2.6, which is too old):

```bash
brew install ruby
export PATH="/opt/homebrew/opt/ruby/bin:$PATH"
bundle install
bundle exec jekyll serve --livereload
```

Site is then at <http://127.0.0.1:4000>.

## Configuration

`_config.yml` holds site identity, avatar, analytics and comment settings.
`_data/contact.yml` controls the sidebar icons.
`_tabs/` holds the static pages (About, Archives, Categories, Tags).

## Theme updates

The theme is a gem, not vendored code. To take a new release:

```bash
bundle update jekyll-theme-chirpy
```
