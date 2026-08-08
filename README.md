# hub

Jekyll source for my research index and notes. Builds on GitHub Pages from
`main`.

Live at https://francose.github.io/hub/

## What goes here

Public work only. The default is off. Something gets listed here after it is
already public somewhere else, not before.

Explicitly out of scope, permanently:

- Employer work product. Internal tooling, detection content, engagement
  detail, and anything authored under an employer's name, including at
  framework level.
- Anything under NDA, legal review, or embargo.
- ThinkEngine production architecture, tenant data, or customer identifiers.
- Coursework, especially anything graded and individual.
- Private repos. If the repo is private, it does not get an entry.

Upstream PR state is asserted on the Projects page. Verify against the GitHub
API before changing merged/open. A hub claiming merged work that isn't is
worse than no hub.

## Structure

- `index.md`: landing
- `research.md`: active areas, each anchored to a public artifact
- `projects.md`: upstream contributions and own tools
- `notes.md`: post index
- `toolkit.md`: tools by job
- `_posts/`: the findings themselves

Nav order is set by `header_pages` in `_config.yml`.

## Format for notes

Short findings, not tutorials. What was tried, what held up, what didn't, and
what I'm not claiming. Under 400 words is the target.
`_drafts/post-template.md` has the shape. Longer pieces are the exception.

## Writing a post

Drop a file in `_posts/` named `YYYY-MM-DD-slug.md`:

```yaml
---
layout: post
title: "Title here"
date: 2026-08-08 20:00:00 -0400
tags: [ai-security]
---
```

Tag with one of `ai-security`, `opsec`, `osint` first, then whatever else is
useful. Small vocabulary is what makes tags worth having.

Anything in `_drafts/` is ignored by the live build.

## Local preview

Optional. GitHub builds the site, so nothing needs to be installed here to
publish. Write markdown, push, done.

Preview requires a Ruby toolchain, which this machine does not have:

```
sudo apt install ruby-full build-essential
bundle install
bundle exec jekyll serve --drafts
```

Then http://127.0.0.1:4000/hub/
