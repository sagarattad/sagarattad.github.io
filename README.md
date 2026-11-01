# sagarattad.github.io
My personal blog

## Preview locally

```bash
bundle exec jekyll serve --livereload
```

Then open http://localhost:4000.

## Writing a post

Create `_posts/YYYY-MM-DD-title.md`:

```markdown
---
title: "Getting Started with ABAP RAP"
description: "One-line summary shown under the title and in post lists."
categories: [RAP, ABAP]
featured: true        # optional, marks it as ★ Featured
read_time: 5          # optional, otherwise estimated from word count
---
```

- **Topics** (the SAP & ABAP / Gen AI / Life filters) come from `categories`.
  Which category belongs to which topic is set in `_data/topics.yml`.
- **Code blocks:** put the language after the opening fence, e.g. ` ```python `.
- **Callouts:** add a class line right under a blockquote:

  ```markdown
  > Use managed implementation unless you have a strong reason not to.
  {: .callout .tip}
  ```

  Types: `.note`, `.tip`, `.warning`.

## Other content

- Projects: `_data/projects.yml`
- Profile photo: add `assets/images/profile.jpg` (initials are shown until then)
- Social preview image: `assets/images/og-image.png`
