# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Artem Zholus's personal website/homepage + research paper list + blog, built with Jekyll and hosted on GitHub Pages (custom domain via `CNAME`). Forked from Jekyll Now, with the design adapted from Jon Barron's website.

## Commands

```bash
bundle install          # install gems (uses vendor/bundle, see .bundle/config)
bundle exec jekyll serve   # serve locally with live rebuild, usually at http://localhost:4000
bundle exec jekyll build   # build static site into _site/
```

There is no test suite or linter in this repo — verify changes by building/serving and checking the rendered pages.

Helper scripts (require ImageMagick):
```bash
./_make_thumbnails.sh   # generates 160x160 thumbnails into tn/images from images/*.png and images/*.jpg
./_make_favicon.sh      # regenerates favicon.ico from images/photo.jpeg
```

## Architecture

Two independent Jekyll collections, both driven by front matter, feed two different parts of the site:

- **`_posts/`** (built-in `posts` collection) — research papers. Permalink pattern `/papers/:year/:month/:day/:title/`, but the homepage (`index.html`) always renders as `/` (see `permalink: /` in `_config.yml`), so per-post pages exist mainly to satisfy Jekyll's collection machinery. `_layouts/default.html` loops over `site.posts` directly to render the "Papers" table on the homepage, reading front matter fields like `title`, `authors`, `venue`, `year`, `image`, `arxiv`, `code`, `website`, `slides`, `poster`, `video`, `hf`, `blogpost`, `openreview` to build the links row for each paper.
- **`_blogposts/`** (custom `blogposts` collection, not currently populated) — blog entries, permalink `/blog/:year/:month/:day/:title/`, layout `blog`. `blog/index.html` loops over `site.blogposts` to render the blog listing page, using `title`, `date`, `author`, `image`, `excerpt`.

Layouts (`_layouts/`):
- `default.html` — the shell for every page. Contains **all** of the homepage HTML inline (bio, "News" list, papers table) gated by `{% if page.url == "/" %}`; for any other page it just wraps `{{ content }}` in `.main-content`. Also conditionally loads MathJax and citation.js when `page.layout == 'blog'`. When updating the homepage bio/news/links, edit this file directly — there is no separate homepage template.
- `post.html` — thin wrapper (`layout: default`) for individual paper pages.
- `blog.html` — wrapper for blog posts: title/date/author header, content, optional bibliography rendered client-side via citation.js from a `page.bibliography` bibtex string front-matter field.

Styling: `style.scss` is compiled by Jekyll's Sass processor (`sass: style: :compressed` in `_config.yml`); `_sass/` holds partials (`_reset`, `_variables`, `_highlights`, `_svg-icons`) though most are currently commented out of the main import chain in `style.scss`.

Images referenced by posts live in `images/`; thumbnails (if generated) go to `tn/images/`.

## Adding content

- New paper: add a file to `_posts/` named `YYYY-MM-DD-slug.markdown` with `layout: post`, `categories: research`, and the front-matter fields described above — it will automatically appear in the homepage papers table (newest first, per Jekyll collection ordering). Look up missing details (authors, venue, links) from the arXiv page and/or project website when the user gives a paper URL instead of asking. Artem Zholus is always wrapped in `<strong>`; co-first authors get a `*` immediately after the name, itself wrapped in `<strong>` (e.g. `<strong>Artem Zholus*</strong>`), matching existing posts.
- New blog post: add a file to `_blogposts/` (create the directory if absent) named `YYYY-MM-DD-slug.markdown` with `layout: blog`.
- After adding/editing a post, run `bundle exec jekyll build` to confirm it renders, then commit and push directly (no need to confirm with the user first for routine content additions like new papers/posts) — push with `git push git@github.com:artemZholus/artemzholus.github.io.git master` since `origin` is an HTTPS remote with no credentials configured in this environment.
