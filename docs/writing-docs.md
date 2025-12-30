---
title: Writing Documentation
nav_order: 3
---

# ✍️ Writing Documentation

All documentation pages are written in **Markdown** and stored in the `docs/` folder of your repository. GitHub Pages automatically converts these files into a clean, navigable website—no build tools required.

## 📁 Adding a New Page

1. In your repository, go to the `docs/` folder.
2. Click **Add file → Create new file**.
3. Name your file with a `.md` extension (e.g., `advanced-setup.md`).
4. Add optional **front matter** at the top (recommended for navigation):

```markdown
---
title: Advanced Setup
nav_order: 5
---
```

5. Write your content using standard Markdown (see below).
6. Commit the file.

✅ Your new page will be live within seconds!

## 🧾 Markdown Syntax Guide

GitHub Pages supports standard [GitHub Flavored Markdown (GFM)](https://docs.github.com/en/get-started/writing-on-github). Here are common examples:

### Headings
```markdown
# Page Title (H1)
## Section (H2)
### Subsection (H3)
```

### Text Formatting
```markdown
*Italic* or _italic_  
**Bold** or __bold__  
~~Strikethrough~~
```

### Lists
```markdown
- Unordered item
- Another item

1. Ordered item
2. Another ordered item
```

### Code
Inline: `` `code` ``

Code block with syntax highlighting:
````
```python
def hello():
    print("Hello, docs!")
```
````

> 💡 Supported languages: `python`, `javascript`, `bash`, `yaml`, `json`, and [many more](https://github.com/github/linguist/blob/master/lib/linguist/languages.yml).

### Links & Images
```markdown
[Link to GitHub](https://github.com)

![Alt text](/assets/images/screenshot.png)
```

> 🔗 **Internal links**: Use relative paths  
> Example: `[Configuration](configuration.md)`

### Tables
```markdown
| Feature     | Supported |
|-------------|:---------:|
| Markdown    | ✅        |
| Jekyll      | ✅        |
| Custom JS   | ❌        |
```

### Emoji
Use GitHub emoji shortcodes:
```markdown
:rocket: :books: :gear: :white_check_mark:
```
→ Renders as: 🚀 📚 ⚙️ ✅

## 🧭 Navigation & Ordering

If your theme supports sidebar navigation (like `jekyll-theme-primer`), use `nav_order` in front matter to control menu order:

```markdown
---
title: Getting Started
nav_order: 1
---
```

Pages without `nav_order` may appear at the bottom or be excluded from navigation.

## 📁 Organizing Large Docs

For complex documentation:

- Keep all pages in `docs/`
- Use clear filenames: `authentication.md`, `api-reference.md`
- Group related topics with consistent headings
- Link between pages liberally

> 📂 **Tip**: Avoid subfolders in `docs/` unless your theme explicitly supports them (many GitHub themes do not).

## 🔍 Previewing Changes

- Every push to the `main` branch updates your live site in ~1 minute.
- To preview locally (optional), install Jekyll:
  ```bash
  gem install bundler jekyll
  jekyll serve
  ```
  Then visit `http://localhost:4000/your-repo-name/`

> ⚠️ Local preview requires the same plugins and theme as GitHub Pages. When in doubt, trust the live site.

---

Now go write amazing docs! 📝✨
```

This version is **clean, correct, and user-focused**—perfect for your GitHub Pages documentation template. ✅
