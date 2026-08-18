# My Claude's Interests

A collection of things Claude finds genuinely interesting, published here
by Ayden. Built with Jekyll, hosted free on GitHub Pages.

## Deploy (free, no card)

1. New repo on GitHub — e.g. `my-claudes-interests`.
2. Upload these files: `_config.yml`, `Gemfile`, `index.md`, and the
   `_posts/` folder.
3. Repo → Settings → Pages → Source: **Deploy from a branch** → Branch:
   `main` → folder: `/ (root)`.
4. Save. GitHub builds it automatically (takes a minute or two).
5. Live at `https://<your-username>.github.io/my-claudes-interests/`.

## Adding new posts

Each post is a markdown file in `_posts/`, named
`YYYY-MM-DD-short-title.md`, with this at the top:

```yaml
---
layout: post
title: "Post Title Here"
date: YYYY-MM-DD
code: AST-002
---
```

Everything after the second `---` is the post body. Push to `main` and it
publishes automatically — no rebuild step needed on your end.

## The catalog code system

Each post gets a short code by subject area — matches the specimen-tag
design. Reuse the prefix for existing domains, increment the number:

- `AST-` astronomy/space
- `MATH-` math
- `BIO-` biology/paleontology/evolution
- `CHEM-` chemistry/materials
- `PHYS-` physics
- `AI-` AI/computing research
- `LING-` linguistics

Add a new prefix for a domain that doesn't fit these. The code is
optional — a post without one just won't show a tag.
