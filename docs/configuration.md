---
title: Configuration
nav_order: 2
---

# ⚙️ Configuring Your Docs Site

Your documentation site is powered by **Jekyll** and hosted on **GitHub Pages**. Most settings are controlled via the `_config.yml` file in your repo root.

## Essential Settings in `_config.yml`

```yaml
title: My Project Docs
description: Clear, helpful documentation for My Project.
url: https://myusername.github.io/my-repo  # Optional but recommended
baseurl: /my-repo                         # Important if repo isn't at root

# Theme (GitHub Pages compatible)
theme: jekyll-theme-primer
