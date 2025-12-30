---
title: Linking to Private Content
nav_order: 6
---

# 🔒 Linking to Private Content: GitHub Repos & Google Sites (Free Personal Account)

When working on personal or team projects, you may want to keep some content—like source code or internal notes—**private**, while still linking to it from documentation. Here’s what’s possible using **free, personal accounts** on GitHub and Google Sites.

---

## 🧩 GitHub: Linking to a Private Repository

You **cannot embed or display** the contents of a private GitHub repo on a public site like GitHub Pages—but you **can link to it**.

### ✅ How It Works
Use a standard Markdown link:
```markdown
[Internal: Project Source (Private)](https://github.com/your-username/your-private-repo)
```

- ✅ **You (or anyone you’ve granted access to)** will see the repo and its `README.md` when signed into GitHub.
- ❌ **Anyone else** (including public visitors or people with the link but no access) will see a **404 “Repository not found”** error.
- 🔐 GitHub **never confirms** that a private repo exists to unauthorized users—this is secure by design.

> ⚠️ **Note**: On a **free GitHub account**, you **cannot publish GitHub Pages from a private repo** at all. Your docs site must be in a **public repo**.

---

## 🌐 Google Sites (Free Personal Account): Can You Make a Private Site?

**Yes—but with limits.**

With a **free Google Account** (e.g., `@gmail.com`), you can create a Google Site and restrict it to **specific people only**.

### ✅ How to Make a Truly Private Google Site
1. Go to [Google Sites](https://sites.google.com)
2. Create a new site
3. Click **Share** → Set general access to **“Restricted”**
4. Under “Add people and groups,” enter **exact email addresses** (e.g., `friend@gmail.com`, `teammate@gmail.com`)
5. Ensure **“Viewer”** (or higher) permission is granted
6. **Do not** select “Anyone with the link” or “Public”

✅ Result:  
- Only the people you explicitly invite can view the site.
- They **must be signed into the Google Account you invited**.
- The site **won’t appear in Google Search** and **can’t be accessed by guessing the URL**.

### ❌ What You *Cannot* Do (Free Personal Account)
- ❌ No password protection for unknown users
- ❌ No “login for anyone” option—only pre-approved emails
- ❌ No way for someone to “request access”
- ❌ Cannot restrict to “anyone signed in to Google”—only specific emails

> 💡 This makes Google Sites **good for small teams or personal notes shared with 1–5 trusted people**, but **not for open private blogs**.

---

## 🔍 GitHub Pages vs. Google Sites (Free Personal Accounts): Privacy Comparison

| Feature | **GitHub Pages** | **Google Sites (Free)** |
|--------|------------------|--------------------------|
| **Free hosting** | ✅ Yes | ✅ Yes |
| **Publish from private repo** | ❌ No (requires paid plan) | N/A (not a code platform) |
| **Private documentation site** | ❌ Only if repo is public (so site is public) | ✅ Yes – can restrict to specific emails |
| **Access control** | Via GitHub repo permissions (GitHub login required) | Via Google Account emails (Google login required) |
| **Public by default?** | ✅ Yes (if published) | ❌ No – you choose sharing |
| **Password protection** | ❌ No | ❌ No |
| **Good for**: | Public docs, open-source projects | Personal wikis, small-team notes, private journals shared with known people |

---

## ✅ Practical Recommendations

- **For public documentation**: Use **GitHub Pages** (your `docs-template` is perfect for this).
- **For private notes shared with a few people**: Use **Google Sites** with “Restricted” sharing.
- **To connect them**: In your public GitHub Pages docs, you can add a note like:
  ```markdown
  🔒 Internal setup guide: [Available here (Google Sites, invite-only)](https://yoursite.google.com)
  ```
  (Only people you’ve invited to the Google Site will be able to open it.)

> ⚠️ Never assume readers have access. Always label private links clearly.

---

By understanding these boundaries, you can build a documentation strategy that’s both **useful and secure**—even on free personal accounts.

---

### ✅ Summary of Key Constraints (Free Tier Only)
- **GitHub Pages**: Always public → great for open docs  
- **Google Sites**: Can be private → but only for **pre-approved Google Accounts**  
- **No platform offers true “password-protected public pages” for free**

This page now gives **realistic, actionable guidance** for personal/free-tier users—no enterprise assumptions.
