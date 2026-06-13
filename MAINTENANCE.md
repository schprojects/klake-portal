# Klake Lake Portal — Maintenance Quick Reference

> For non-tech users. Print this page or bookmark it.

---

## Site URL

```
https://your-org.github.io/klake-portal/
```

---

## How to Add a News Post

1. Go to your repo: `github.com/your-org/klake-portal`
2. Click `content/news/`
3. Click **"Add file" → "Create new file"**
4. Name it: `short-title.md` (e.g., `tree-plantation-event.md`)
5. Paste this template:

```markdown
---
title: "Your Post Title"
date: 2026-06-13
---

Write your content here in plain text.
```

6. Click **"Commit changes"**

---

## How to Add a Photo to the Gallery

**Step 1:** Upload the full photo to Google Drive → `Photos/Gallery/` → set sharing to "Anyone with the link" → copy link.

**Step 2:** Resize a copy to ~400px wide using https://imageresizer.com, save as `photo-name.jpg`.

**Step 3:** Upload the thumbnail: GitHub → `static/images/thumbnails/gallery/` → **"Add file" → "Upload files"** → select thumbnail → **"Commit changes"**.

**Step 4:** Edit `content/media/gallery.md` → add:

```markdown
## Photo Title

[![description](/images/thumbnails/gallery/photo-name.jpg)](GOOGLE_DRIVE_LINK)
```

---

## How to Add a Legal Document

1. Upload PDF to Google Drive → `Legal Documents/` subfolder
2. Set sharing to "Anyone with the link" → copy link
3. Edit `content/legal/records.md` → add new list item with link

---

## How to Update Contacts

Edit `content/contacts/staff.md` or `content/contacts/volunteers.md` and update the tables/lists.

---

## What NOT to Do

- Do not delete or rename files in `layouts/`
- Do not change `config.toml`
- Do not delete `.github/workflows/`
- Do not rename folders in `content/`

---

## What If Something Breaks?

1. Check if the site still loads at the URL above
2. If changes aren't showing after 2 minutes, go to GitHub → **"Actions"** tab
3. If there's a red ✗, click it to see the error message
4. Screenshot the error and send to a tech contributor
