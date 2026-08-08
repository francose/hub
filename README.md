# osint-blog

Jekyll source for my OSINT notes. Builds on GitHub Pages from `main`.

Live at https://francose.github.io/osint-blog/

## Writing a post

Drop a file in `_posts/` named `YYYY-MM-DD-slug.md` with front matter:

```yaml
---
layout: post
title: "Title here"
date: 2026-08-07 20:00:00 -0400
tags: [osint, methodology]
---
```

Anything in `_drafts/` is ignored by the live build. Preview drafts locally with
`bundle exec jekyll serve --drafts`.

Push to `main` and Pages rebuilds. Takes about a minute.

## Local preview

```
bundle install
bundle exec jekyll serve
```

Then http://127.0.0.1:4000/osint-blog/

## Notes

Sanitize before you commit. Case material, client names, subject PII, and
anything from an engagement under NDA does not belong in a public repo, and git
history keeps what you delete in a later commit.
