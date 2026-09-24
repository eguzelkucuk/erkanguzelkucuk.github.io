---
title: "Hello, world"
date: 2026-09-24 10:00:00 +0300
categories: [Meta]
tags: [en, jekyll]
description: Why this blog exists and how it is put together.
pin: true
---

I've been meaning to write things down for years. Notes scattered across Jira tickets, Slack threads and
`TODO.md` files are not notes — they're archaeology. So: a blog.

## What goes here

Mostly things I had to figure out at work and would rather not figure out twice:

- .NET / microservice architecture decisions and their consequences
- Frontend work — module federation, build pipelines, the usual pain
- Debugging stories where the root cause was embarrassing
- Small tooling wins

## Two languages

Posts are written in **English** or **Turkish**, whichever fits the topic. Every post carries a language
tag, so you can read only [`en`](/tags/en/) or only [`tr`](/tags/tr/) if you prefer.

## How the site is built

Static site, no database, no server to patch:

| Piece | Choice |
| --- | --- |
| Generator | [Jekyll](https://jekyllrb.com) |
| Theme | [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) |
| Build | GitHub Actions |
| Hosting | GitHub Pages |

Writing a post is one Markdown file in `_posts/`{: .filepath }, a `git push`, and Actions does the rest.

```bash
git add _posts/2026-09-24-my-post.md
git commit -m "post: my post"
git push
```

> Every post needs a filename in the form `YYYY-MM-DD-slug.md`. Jekyll silently ignores anything that
> doesn't match, and future-dated posts won't be published until that date.
{: .prompt-warning }

That's the whole system. Let's see how long I keep it up.
