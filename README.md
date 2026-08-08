# osint-blog

Jekyll source for my notes on AI security research, OPSEC, and OSINT. Builds on
GitHub Pages from `main`.

Live at https://francose.github.io/osint-blog/

## Format

Short findings, not tutorials. What was tried, what held up, what didn't, and
what I'm not claiming. Under 400 words is the target — `_drafts/post-template.md`
has the shape. Longer pieces are the exception, not the default.

## Writing a post

Drop a file in `_posts/` named `YYYY-MM-DD-slug.md` with front matter:

```yaml
---
layout: post
title: "Title here"
date: 2026-08-07 20:00:00 -0400
tags: [ai-security]
---
```

Tag with one of `ai-security`, `opsec`, `osint` as the first tag, then whatever
else is useful. Keeping the vocabulary small is what makes the tags worth
having.

Anything in `_drafts/` is ignored by the live build. Preview drafts locally with
`bundle exec jekyll serve --drafts`.

Push to `main` and Pages rebuilds. Takes about a minute.

## Local preview

Optional. GitHub builds the site, so nothing needs to be installed here to
publish — write markdown, push, done.

Preview requires a Ruby toolchain, which this machine does not have:

```
sudo apt install ruby-full build-essential
bundle install
bundle exec jekyll serve --drafts
```

Then http://127.0.0.1:4000/osint-blog/

## Notes

Sanitize before you commit. Case material, client names, subject PII, and
anything from an engagement under NDA does not belong in a public repo, and git
history keeps what you delete in a later commit.
