# Klake Lake Portal — Complete Implementation Report

> A comprehensive guide to building, deploying, and maintaining a free website for lake rejuvenation and maintenance.

---

## Table of Contents

1. [Introduction & Goals](#1-introduction--goals)
2. [What Are We Building?](#2-what-are-we-building)
3. [Technology Choices Explained](#3-technology-choices-explained)
4. [How It All Fits Together](#4-how-it-all-fits-together)
5. [Site Structure](#5-site-structure)
6. [File Storage Strategy](#6-file-storage-strategy)
7. [Step-by-Step Setup Guide](#7-step-by-step-setup-guide)
8. [Adding Content (Non-Tech Workflow)](#8-adding-content-non-tech-workflow)
9. [Backup Strategy](#9-backup-strategy)
10. [Maintenance Guide](#10-maintenance-guide)
11. [Cost Breakdown](#11-cost-breakdown)
12. [Glossary](#12-glossary)

---

## 1. Introduction & Goals

### What is this project?

We are building a **free website** for Klake Lake — a community-driven lake rejuvenation and maintenance initiative. The website will serve as the central online presence for the lake, providing:

- Legal records and documentation
- Contact information for officials and volunteers
- Photo galleries and media coverage
- News and events updates

### Who is this guide for?

This guide is written for **two types of people**:

| Person | Skill Level | What They Do |
|--------|------------|--------------|
| **Non-tech maintainer** | Knows how to use a browser, email, and upload files | Adds photos, news posts, and legal documents |
| **Tech contributor** | Has some familiarity with GitHub, command line, or web development | Sets up the site initially and handles major changes |

### Guiding Principles

The following requirements from the project brief drove every design decision:

| Requirement | How We Addressed It |
|------------|-------------------|
| Low economic burden | Used entirely free services ($0/year) |
| Easily maintainable by non-tech people | GitHub web interface + Google Drive — no software to install |
| Extensible for future | Hugo content is folder-based; adding a new section means creating a new folder |
| Media storage | Master copies on Google Drive (free 15GB), thumbnails in GitHub repo |
| Legal documents | PDFs stored on Google Drive, linked from the website |
| Picture gallery | Thumbnails load fast from GitHub; full-resolution on Google Drive |

---

## 2. What Are We Building?

### A Static Website

Most websites you visit (Facebook, Twitter, WordPress sites) are **dynamic** — they have a database behind them. When you visit a page, the server assembles the page on-the-fly from data in the database.

Our site is **static**. This means:

- Every page is a pre-built HTML file stored on a server
- When someone visits, the server just sends the file — no assembly needed
- This is **much faster**, **much cheaper** (can be free), and **much more secure**

Think of it like a book vs a news ticker. Our site is a book — once printed, it stays the same until we print a new edition. When we update content, we rebuild the whole book (takes about 30 seconds) and replace all pages.

### The Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    Google Drive                          │
│   (Master copies: full-resolution photos, legal PDFs)   │
└────────────────────┬────────────────────────────────────┘
                     │ (links embedded in pages)
                     ▼
┌─────────────────────────────────────────────────────────┐
│              GitHub Repository (klake-portal)             │
│  ┌──────────┐  ┌───────────┐  ┌────────────────────┐   │
│  │ Content  │  │ Thumbnails│  │ Theme/Templates    │   │
│  │ (markdown)│  │ (small JPGs)│  │ (Hugo theme)      │   │
│  └──────────┘  └───────────┘  └────────────────────┘   │
└────────────────────┬────────────────────────────────────┘
                     │ (auto-built by GitHub Actions)
                     ▼
┌─────────────────────────────────────────────────────────┐
│              GitHub Pages (live website)                 │
│   https://your-org.github.io/klake-portal/               │
└─────────────────────────────────────────────────────────┘
```

**Step by step flow:**

1. A non-tech person uploads a photo to Google Drive and its thumbnail to the GitHub repo
2. GitHub Actions (an automated system) detects the change
3. It runs Hugo to rebuild the entire website with the new content
4. The live site updates automatically (takes about 30-60 seconds)

### What Technologies Are We Using?

| Technology | What It Does | Why We Chose It |
|-----------|-------------|-----------------|
| **Hugo** | Static site generator — converts markdown files into HTML pages | Fastest, single binary, simple folder-based structure |
| **GitHub Pages** | Free web hosting service | Free, automatic HTTPS, works seamlessly with Hugo |
| **GitHub Actions** | Automated task runner | Builds and deploys the site automatically on every change |
| **Google Drive** | File storage for master copies | 15GB free, everyone knows how to use it, no setup |
| **YouTube** | Video hosting | Free, embeddable, ubiquitous |
| **Git** | Version control — tracks every change ever made | Free, industry standard |

### What is a Static Site Generator?

A **Static Site Generator** (SSG) is a tool that takes simple text files (written in **markdown** — a plain text format) and converts them into a complete website with HTML, CSS, and navigation.

Instead of writing `<html><body><h1>Hello</h1></body></html>` for every page, you just write:

```markdown
# Hello
This is a paragraph.
```

And Hugo turns it into proper HTML automatically. This is the key to making the site **easy to maintain for non-tech people** — they write in plain text (almost like writing an email) and Hugo handles all the technical formatting.

---

## 3. Technology Choices Explained

### Why Hugo and Not WordPress?

| Factor | Hugo | WordPress |
|--------|------|-----------|
| Cost | $0 | $5-30/month for hosting |
| Security | No database, no attacks | Constantly targeted by hackers |
| Speed | Blazing fast (loads in <1 second) | Can be slow without optimization |
| Updates | Edit files, auto-rebuild | Login to admin panel |
| Backup | Entire site is a folder of files | Need database dumps + file backups |
| Non-tech friendly | GitHub web UI + Google Drive | Admin dashboard (but costs money) |

**Verdict:** For a community project with zero budget and simple content needs, Hugo is the ideal choice. WordPress would be better if the site needed user logins, comments, or dynamic features — but those cost money and need constant maintenance.

### Why Hugo and Not Jekyll?

Both are free and work on GitHub Pages. Key differences:

| Factor | Hugo | Jekyll |
|--------|------|--------|
| Language | Go (single binary) | Ruby (needs Ruby installed) |
| Speed | ~1ms per page | ~200ms per page |
| Install | Download one file | Install Ruby + gems |
| GitHub Pages native? | Needs GitHub Actions | Built-in (older version) |

**Verdict:** Hugo is simpler to install, faster, and easier to work with. Jekyll's "built-in" GitHub Pages support means you get an older, slower version with limited plugins.

### Why GitHub Pages and Not Netlify or Vercel?

All three are free. GitHub Pages wins for this project because:

- **You're already using GitHub** — one less account to manage
- **Unlimited bandwidth** — Netlify's free tier has bandwidth limits
- **Automatic HTTPS** — free SSL certificate included
- **Simple** — one button to enable

### Why Google Drive and Not a Database?

A database would mean:
- Monthly hosting costs ($5-15/month)
- Security vulnerabilities
- Complex backup procedures
- Harder for non-tech people to update

Google Drive:
- Everyone knows how to use it
- Free (15GB)
- Google handles backups and security
- No integration needed — just paste links

### Why Thumbnails in GitHub Repo?

If gallery images were served directly from Google Drive:

1. They would load slower (Google Drive isn't optimized for image serving)
2. Google Drive links can sometimes require authentication
3. Page load times would suffer

By keeping **small thumbnails** (300-500px wide, ~50-100KB each) in the GitHub repo, they:
- Load instantly from GitHub Pages
- Display nicely in the gallery grid
- Link to the full-resolution master on Google Drive

---

## 4. How It All Fits Together

### Repository Layout (on GitHub)

This is the complete folder structure of the project. Every file has a purpose.

```
klake-portal/
│
├── .github/
│   └── workflows/
│       ├── hugo-deploy.yml        # Auto-builds and deploys the site
│       └── backup.yml             # Weekly backup routine
│
├── content/                        # ALL text content (markdown files)
│   ├── _index.md                  # Homepage
│   ├── about.md                   # About the lake
│   ├── legal/
│   │   ├── _index.md              # Legal records landing page
│   │   ├── records.md             # List of legal documents (links to Drive)
│   │   └── developments.md        # Current legal developments (text updates)
│   ├── contacts/
│   │   ├── _index.md              # Contacts landing page
│   │   ├── staff.md               # Officials table (engineer, guard, etc.)
│   │   └── volunteers.md          # Volunteer email list
│   ├── media/
│   │   ├── _index.md              # Media landing page
│   │   ├── coverage.md            # News articles, TV coverage, YouTube embeds
│   │   └── gallery.md             # Photo gallery (references thumbnails)
│   └── news/
│       ├── _index.md              # News listing page
│       └── first-post.md          # Example news article
│
├── static/                         # Files copied directly to live site
│   ├── images/
│   │   ├── thumbnails/            # ← PHOTO THUMBNAILS go here
│   │   │   ├── gallery/           # Gallery thumbnails
│   │   │   └── news/              # News article thumbnails
│   │   ├── banners/               # Homepage hero images
│   │   └── icons/                 # Site icons (favicon, etc.)
│   ├── documents/                 # ← SMALL PDFs only (<5MB each, <100MB total)
│   └── css/                       # Custom styles (if needed)
│
├── layouts/                        # Hugo templates (HTML structure)
│   ├── _default/
│   │   ├── baseof.html            # Base layout (header, footer, nav)
│   │   ├── list.html              # Layout for list pages (news listing)
│   │   └── single.html            # Layout for individual pages
│   ├── partials/
│   │   ├── header.html            # Site header / navigation bar
│   │   ├── footer.html            # Site footer
│   │   └── gallery.html           # Gallery grid component
│   └── index.html                 # Homepage layout
│
├── static/images/thumbnails/       # ← MAPS TO SITE ROOT
│   (see note below)               #    /images/thumbnails/gallery/photo.jpg
│
├── config.toml                    # Hugo configuration file
├── REPORT.md                      # This document
├── MAINTENANCE.md                 # 1-page quick reference for non-tech folks
└── README.md                      # GitHub repo landing page
```

### Understanding the `static/` Folder

Hugo treats the `static/` folder specially — anything inside it is copied **as-is** to the root of the built website. This means:

| File in Repo | Live Site URL |
|-------------|---------------|
| `static/images/thumbnails/gallery/lake.jpg` | `https://site.org/images/thumbnails/gallery/lake.jpg` |
| `static/images/banners/hero.jpg` | `https://site.org/images/banners/hero.jpg` |
| `static/documents/minutes-2024.pdf` | `https://site.org/documents/minutes-2024.pdf` |

This is why we store thumbnails in `static/` — they become directly accessible URLs on the live site.

### The Content/ Folder (All Text Lives Here)

The `content/` folder mirrors the site structure. Each folder becomes a section, each `.md` file becomes a page.

```
content/                          Live Site
────────                          ────────
content/_index.md      ─────►     / (homepage)
content/about.md       ─────►     /about/
content/legal/                     /legal/
content/legal/records.md ─────►   /legal/records/
content/news/                      /news/
content/news/first-post.md ─────► /news/first-post/
```

### How Links Work

From within markdown files, you reference other pages like this:

```markdown
[View legal records](/legal/records/)
[Photo gallery](/media/gallery/)
[Download PDF](/documents/water-rights.pdf)
[Full resolution photo](https://drive.google.com/file/d/ABC123/view)
```

Notice that internal links (to pages and files within the site) use **relative paths** starting with `/`. External links (Google Drive) use the **full URL**.

---

## 5. Site Structure

### Pages and Their Purpose

| Page | URL | Purpose | Content Type |
|------|-----|---------|-------------|
| Home | `/` | Welcome, quick links to all sections | Markdown + banners |
| About | `/about/` | Lake history, geography, significance | Markdown text |
| Legal Records | `/legal/records/` | List of legal documents (water rights, etc.) | Links to Google Drive PDFs |
| Legal Developments | `/legal/developments/` | Updates on court cases, policies | Markdown text (blog-style) |
| Staff Contacts | `/contacts/staff/` | Engineer, guard, garbage section, officials | Table (name, role, phone, email) |
| Volunteers | `/contacts/volunteers/` | Volunteer email IDs | List or table |
| Media Coverage | `/media/coverage/` | News articles, TV coverage | Embedded YouTube + links |
| Gallery | `/media/gallery/` | Photo gallery with captions | Thumbnail grid → Drive links |
| News & Events | `/news/` | Announcements and event updates | Blog posts (chronological) |

### Navigation Bar

The top navigation will have links to all major sections:

```
[Home] [About] [Legal] [Contacts] [Media] [News]
```

Dropdowns can be set up for sections with sub-pages (e.g., Legal → Records, Developments).

### Design Considerations

- **Mobile-first** — The Hugo theme will be responsive (works on phones and desktops)
- **Accessible** — Proper heading structure, alt text on images
- **Print-friendly** — Legal documents should be printable
- **Fast loading** — All pages under 1 second load time

---

## 6. File Storage Strategy

This is the most important design decision. Here is exactly **what goes where and why**.

### The Two-Tier System

```
                    ┌─────────────────────────────────────┐
                    │     Google Drive (Master Copies)     │
                    │                                     │
                    │  /Klake Lake Portal/                 │
                    │  ├── Photos/                         │
                    │  │   ├── Gallery/      ← Full-res JPGs │
                    │  │   └── News/         ← Full-res JPGs │
                    │  ├── Legal Documents/                │
                    │  │   ├── Water Rights/               │
                    │  │   ├── Conservation Orders/        │
                    │  │   └── Meeting Minutes/            │
                    │  └── Media Coverage/                 │
                    │                                      │
                    │  Total: ~15GB free                   │
                    └─────────────────────────────────────┘
                                    │
                        (links embedded in pages)
                                    │
                                    ▼
┌──────────────────────────────────────────────────────────┐
│                 GitHub Repo (Thumbnails + Content)        │
│                                                          │
│  static/images/thumbnails/     ← 300-500px JPGs (~50KB) │
│  static/documents/             ← Small PDFs only (<5MB)  │
│  content/                      ← All text (markdown)     │
│                                                          │
│  Total: ~100MB (easily fits GitHub's 1GB limit)         │
└──────────────────────────────────────────────────────────┘
```

### What Goes Where — Complete Rules

| File Type | Master Copy | Thumbnail/Copy | Why |
|-----------|------------|----------------|-----|
| **Gallery photos** (high-res) | Google Drive → `Photos/Gallery/` | GitHub → `static/images/thumbnails/gallery/` | Thumbnails load fast; originals too large for git |
| **News photos** (high-res) | Google Drive → `Photos/News/` | GitHub → `static/images/thumbnails/news/` | Same reasoning |
| **Legal PDFs** | Google Drive → `Legal Documents/` | Only link from site (no copy) | PDFs can be 50-200MB, exceed GitHub limits |
| **Small PDFs** (<5MB) | Google Drive (backup) | GitHub → `static/documents/` | Small enough for git, convenient direct link |
| **Videos** | YouTube | YouTube embed code | YouTube handles video hosting for free |
| **Text content** | GitHub → `content/` folder | Same (only one copy needed) | Text is tiny, belongs in git |
| **Banners/hero images** | GitHub → `static/images/banners/` | Same (only one copy needed) | Small files optimized for web |

### Google Drive Folder Structure

Create this exact folder structure in Google Drive:

```
Klake Lake Portal/
├── Photos/
│   ├── Gallery/
│   │   ├── lake-sunset.jpg
│   │   ├── clean-up-drive-2025.jpg
│   │   └── tree-plantation-event.jpg
│   └── News/
│       └── inauguration-ceremony.jpg
├── Legal Documents/
│   ├── Water Rights/
│   │   ├── water-rights-certificate-1998.pdf
│   │   └── amendment-2005.pdf
│   ├── Conservation Orders/
│   │   └── conservation-order-2024.pdf
│   └── Meeting Minutes/
│       ├── minutes-2024-01.pdf
│       └── minutes-2024-02.pdf
└── Media Coverage/
    └── newspaper-clipping-2024.jpg
```

### How to Get Shareable Links from Google Drive

1. Right-click the file in Google Drive
2. Click **"Share"** → **"Anyone with the link"**
3. Click **"Copy link"**
4. The link looks like: `https://drive.google.com/file/d/ABC123xyz/view`
5. Paste this into your markdown file as:

```markdown
[Download Water Rights Certificate (PDF)](https://drive.google.com/file/d/ABC123xyz/view)
```

**TIP:** If you want to embed an image directly from Google Drive (not recommended for speed), you cannot use the standard share link. Instead, you'd need a direct image URL. But for our project, we use thumbnails from GitHub and link to the Drive master, so this isn't needed.

---

## 7. Step-by-Step Setup Guide

This section takes you from **zero** (no accounts created) to **live website**. Follow these steps in order.

### Phase 1: Create Accounts

**Step 1: Create a GitHub Account**

1. Go to https://github.com
2. Click **"Sign up"**
3. Enter an email address (use a shared project email if possible, e.g., `klakelake@gmail.com`)
4. Create a username (e.g., `klake-portal`)
5. Choose the **free plan**
6. Verify your email

**Step 2: Create a Google Account (for Drive)**

If you don't already have one, create a dedicated account for the project:
1. Go to https://drive.google.com
2. Click **"Create account"** → **"For my personal use"**
3. Use the name "Klake Lake Portal"
4. This gives you 15GB free storage

**Step 3: Create a YouTube Channel**

1. Go to https://youtube.com
2. Sign in with the same Google account
3. Click your profile → **"Create a channel"**
4. Name it "Klake Lake"

### Phase 2: Create the GitHub Repository

**Step 4: Create a New Repository**

1. Go to https://github.com (logged in)
2. Click the **"+"** icon in the top-right → **"New repository"**
3. Fill in:
   - **Repository name:** `klake-portal`
   - **Description:** "Website for Klake Lake rejuvenation and maintenance"
   - **Visibility:** Public (free; private repos also work but need paid GitHub Pages)
   - **Initialize with README:** Check this
   - **Add .gitignore:** Select `None`
   - **Choose a license:** `MIT License` (or leave as None)
4. Click **"Create repository"**

**Step 5: Enable GitHub Pages**

1. Go to your repo: `https://github.com/your-org/klake-portal`
2. Click **"Settings"** tab
3. In the left sidebar, click **"Pages"**
4. Under **"Source"**, select **"GitHub Actions"** (we'll let Actions handle deployment)
5. Leave this page open — we'll come back after the first build

### Phase 3: Set Up Hugo Project Structure

**Step 6: Create the Project Files**

Now we'll create the folder structure and files. We can do this entirely through the GitHub web interface (no command line needed).

**Option A: Using the command line (if you have Git installed):**

```bash
# Clone the empty repo
git clone https://github.com/your-org/klake-portal.git
cd klake-portal

# Create the folder structure
mkdir -p content/legal
mkdir -p content/contacts
mkdir -p content/media
mkdir -p content/news
mkdir -p static/images/thumbnails/gallery
mkdir -p static/images/thumbnails/news
mkdir -p static/images/banners
mkdir -p static/images/icons
mkdir -p static/documents
mkdir -p layouts/_default
mkdir -p layouts/partials
mkdir -p .github/workflows
```

**Option B: Using the GitHub web interface:**

1. On your repo page, click **"Add file"** → **"Create new file"**
2. In the filename field, type: `content/legal/.gitkeep`
3. Put a dot (`.`) or nothing in the content
4. Click **"Commit new file"**
5. Repeat for each folder you need to create:
   - `content/contacts/.gitkeep`
   - `content/media/.gitkeep`
   - `content/news/.gitkeep`
   - `static/images/thumbnails/gallery/.gitkeep`
   - `static/images/thumbnails/news/.gitkeep`
   - `static/images/banners/.gitkeep`
   - `static/documents/.gitkeep`
   - `layouts/_default/.gitkeep`
   - `layouts/partials/.gitkeep`
   - `.github/workflows/.gitkeep`

**Step 7: Create the Hugo Configuration File**

Create a file called `config.toml` at the root of your repo with this content:

```toml
baseURL = "https://your-org.github.io/klake-portal/"
languageCode = "en-us"
title = "Klake Lake Portal"
theme = "klake"  # We'll create this as a custom theme

# Menu / Navigation
[menu]
  [[menu.main]]
    identifier = "home"
    name = "Home"
    url = "/"
    weight = 1
  [[menu.main]]
    identifier = "about"
    name = "About"
    url = "/about/"
    weight = 2
  [[menu.main]]
    identifier = "legal"
    name = "Legal"
    url = "/legal/"
    weight = 3
  [[menu.main]]
    identifier = "contacts"
    name = "Contacts"
    url = "/contacts/"
    weight = 4
  [[menu.main]]
    identifier = "media"
    name = "Media"
    url = "/media/"
    weight = 5
  [[menu.main]]
    identifier = "news"
    name = "News"
    url = "/news/"
    weight = 6

# Taxonomies (tags, categories — useful for news)
[taxonomies]
  tag = "tags"
  category = "categories"

# Markdown options
[markup]
  [markup.goldmark]
    [markup.goldmark.renderer]
      unsafe = true  # Allows HTML in markdown (for embeds)
```

Replace `your-org` with your actual GitHub username or organization name.

### Phase 4: Create the Hugo Theme

Instead of using a pre-built theme (which adds complexity), we'll create a minimal custom theme. This keeps the site lean and easy to understand.

**Step 8: Create Base Layout**

Create `layouts/_default/baseof.html`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ if .Title }}{{ .Title }} | {{ end }}{{ .Site.Title }}</title>
    <link rel="stylesheet" href="/css/style.css">
</head>
<body>
    <header>
        <nav>
            <a href="/" class="logo">{{ .Site.Title }}</a>
            <ul>
                {{ range .Site.Menus.main }}
                <li><a href="{{ .URL }}">{{ .Name }}</a></li>
                {{ end }}
            </ul>
        </nav>
    </header>

    <main>
        {{ block "main" . }}{{ end }}
    </main>

    <footer>
        <p>&copy; {{ now.Year }} Klake Lake Community. Free to share.</p>
    </footer>
</body>
</html>
```

Create `layouts/_default/single.html`:

```html
{{ define "main" }}
<article>
    <h1>{{ .Title }}</h1>
    {{ if .Date }}
    <time>{{ .Date.Format "January 2, 2006" }}</time>
    {{ end }}
    <div class="content">
        {{ .Content }}
    </div>
</article>
{{ end }}
```

Create `layouts/_default/list.html`:

```html
{{ define "main" }}
<section>
    <h1>{{ .Title }}</h1>
    <div class="content">
        {{ .Content }}
    </div>
    <ul class="page-list">
        {{ range .Pages }}
        <li>
            <a href="{{ .RelPermalink }}">{{ .Title }}</a>
            {{ if .Date }}
            <time>{{ .Date.Format "January 2, 2006" }}</time>
            {{ end }}
        </li>
        {{ end }}
    </ul>
</section>
{{ end }}
```

**Step 9: Create Homepage Layout**

Create `layouts/index.html`:

```html
{{ define "main" }}
<section class="hero">
    <h1>Welcome to Klake Lake</h1>
    <p>A community-driven initiative for lake rejuvenation and maintenance.</p>
    <div class="hero-buttons">
        <a href="/about/" class="button">Learn More</a>
        <a href="/contacts/volunteers/" class="button">Get Involved</a>
    </div>
</section>

<section class="quick-links">
    <h2>Quick Links</h2>
    <div class="card-grid">
        <div class="card">
            <h3><a href="/legal/">Legal Records</a></h3>
            <p>Lake documentation, rights, and legal developments.</p>
        </div>
        <div class="card">
            <h3><a href="/contacts/">Contacts</a></h3>
            <p>Find officials, staff, and volunteer contacts.</p>
        </div>
        <div class="card">
            <h3><a href="/media/gallery/">Photo Gallery</a></h3>
            <p>See the beauty of Klake Lake.</p>
        </div>
        <div class="card">
            <h3><a href="/news/">News & Events</a></h3>
            <p>Latest updates and upcoming events.</p>
        </div>
    </div>
</section>
{{ end }}
```

**Step 10: Create CSS**

Create `static/css/style.css`:

```css
/* Klake Lake Portal - Minimal Styles */
* { margin: 0; padding: 0; box-sizing: border-box; }
body { font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif; line-height: 1.6; color: #333; max-width: 960px; margin: 0 auto; padding: 1rem; }
header { border-bottom: 2px solid #2d7a3a; margin-bottom: 2rem; padding-bottom: 1rem; }
nav { display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; }
nav .logo { font-size: 1.3rem; font-weight: bold; color: #2d7a3a; text-decoration: none; }
nav ul { list-style: none; display: flex; gap: 1rem; }
nav a { color: #2d7a3a; text-decoration: none; }
nav a:hover { text-decoration: underline; }
main { min-height: 60vh; }
h1 { font-size: 2rem; margin-bottom: 1rem; color: #1a472a; }
h2 { font-size: 1.5rem; margin: 1.5rem 0 0.5rem; color: #2d7a3a; }
h3 { font-size: 1.2rem; margin: 1rem 0 0.5rem; }
p, li { margin-bottom: 0.5rem; }
a { color: #1a6b3a; }
img { max-width: 100%; height: auto; }
table { width: 100%; border-collapse: collapse; margin: 1rem 0; }
th, td { border: 1px solid #ddd; padding: 0.5rem; text-align: left; }
th { background: #e8f5e9; }
.hero { text-align: center; padding: 3rem 1rem; background: #e8f5e9; border-radius: 8px; margin-bottom: 2rem; }
.hero h1 { margin-bottom: 0.5rem; }
.hero-buttons { margin-top: 1.5rem; }
.button { display: inline-block; padding: 0.6rem 1.2rem; background: #2d7a3a; color: white; text-decoration: none; border-radius: 4px; margin: 0.25rem; }
.button:hover { background: #1a472a; }
.card-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 1rem; margin: 1rem 0; }
.card { border: 1px solid #ddd; padding: 1rem; border-radius: 6px; }
.page-list { list-style: none; }
.page-list li { padding: 0.5rem 0; border-bottom: 1px solid #eee; }
.gallery-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(200px, 1fr)); gap: 1rem; }
.gallery-grid img { border-radius: 4px; width: 100%; height: 200px; object-fit: cover; }
.gallery-grid figure { margin: 0; }
.gallery-grid figcaption { font-size: 0.9rem; text-align: center; margin-top: 0.25rem; color: #666; }
time { color: #666; font-size: 0.9rem; }
footer { border-top: 1px solid #ddd; margin-top: 3rem; padding-top: 1rem; text-align: center; color: #888; font-size: 0.9rem; }
@media (max-width: 600px) { nav { flex-direction: column; gap: 0.5rem; } }
```

### Phase 5: Create Content Files

**Step 11: Create Homepage Content**

Create `content/_index.md`:

```markdown
---
title: "Home"
---

Welcome to the official website of **Klake Lake** — a community-driven initiative
dedicated to the rejuvenation and maintenance of our beloved lake.

Use the navigation above to explore legal records, find contact information,
browse photos, and stay updated with the latest news and events.
```

**Step 12: Create About Page**

Create `content/about.md`:

```markdown
---
title: "About Klake Lake"
---

Klake Lake is a vital water body in our community, supporting local biodiversity
and serving as a source of livelihood for many. The lake rejuvenation project
is a community effort involving local residents, environmentalists, and
government officials.

## Our Mission

To restore and maintain Klake Lake as a sustainable ecosystem that benefits
both nature and the community.

## History

[Add a brief history of the lake — when it was created, its significance, etc.]

## Geography

[Add details about the lake's location, size, depth, catchment area, etc.]
```

**Step 13: Create Legal Records Page**

Create `content/legal/_index.md`:

```markdown
---
title: "Legal"
---

This section contains legal records and ongoing legal developments related
to Klake Lake.
```

Create `content/legal/records.md`:

```markdown
---
title: "Legal Records"
---

Below is a list of legal documents related to Klake Lake. Click a document
to view or download it.

## Water Rights

- [Water Rights Certificate 1998 (PDF)](https://drive.google.com/file/d/REPLACE_WITH_YOUR_FILE_ID/view)
- [Amendment to Water Rights 2005 (PDF)](https://drive.google.com/file/d/REPLACE_WITH_YOUR_FILE_ID/view)

## Conservation Orders

- [Conservation Order 2024 (PDF)](https://drive.google.com/file/d/REPLACE_WITH_YOUR_FILE_ID/view)

## Meeting Minutes

- [Minutes — January 2024 (PDF)](https://drive.google.com/file/d/REPLACE_WITH_YOUR_FILE_ID/view)
- [Minutes — February 2024 (PDF)](https://drive.google.com/file/d/REPLACE_WITH_YOUR_FILE_ID/view)
```

**IMPORTANT:** Replace `REPLACE_WITH_YOUR_FILE_ID` with the actual Google Drive file IDs.

Create `content/legal/developments.md`:

```markdown
---
title: "Current Legal Developments"
---

## Ongoing Court Cases

*No ongoing cases at this time.*

## Recent Orders

*No recent orders.*

## Policy Updates

*No recent policy updates.*

---

*This page was last updated on June 13, 2026.*
```

**Step 14: Create Contacts Pages**

Create `content/contacts/_index.md`:

```markdown
---
title: "Contacts"
---

Find contact information for lake officials, staff, and volunteers.
```

Create `content/contacts/staff.md`:

```markdown
---
title: "Staff Contacts"
---

| Role | Name | Phone | Email |
|------|------|-------|-------|
| Engineer | [Name] | [Phone] | [Email] |
| Guard | [Name] | [Phone] | [Email] |
| Garbage Section | [Name] | [Phone] | [Email] |
| Higher Official 1 | [Name] | [Phone] | [Email] |
| Higher Official 2 | [Name] | [Phone] | [Email] |
```

Create `content/contacts/volunteers.md`:

```markdown
---
title: "Volunteers"
---

Join our community effort! Contact any of our volunteers below.

## Volunteer Email Contacts

- [name1@example.com](mailto:name1@example.com)
- [name2@example.com](mailto:name2@example.com)
- [name3@example.com](mailto:name3@example.com)

---

*To add or remove your email, contact the website maintainer.*
```

**Step 15: Create Media Pages**

Create `content/media/_index.md`:

```markdown
---
title: "Media"
---

Photos, videos, and news coverage of Klake Lake and its rejuvenation activities.
```

Create `content/media/coverage.md`:

```markdown
---
title: "Media Coverage"
---

## News Articles

*Coming soon.*

## Video Coverage

*Coming soon.*

<!-- To embed a YouTube video, paste the embed code here:
<iframe width="560" height="315" src="https://www.youtube.com/embed/VIDEO_ID" title="Video title" frameborder="0" allowfullscreen></iframe>
-->
```

Create `content/media/gallery.md`:

```markdown
---
title: "Photo Gallery"
---

{{< gallery >}}

## How to add photos

To add a photo:
1. Upload the **full-resolution original** to Google Drive (`Photos/Gallery/`)
2. Resize a copy to 300-500px wide (thumbnail)
3. Upload the thumbnail to `static/images/thumbnails/gallery/` in the GitHub repo
4. Edit this page and add an entry below

<!-- Add gallery entries below this line -->

---

### Lake at Sunset

[![Lake at sunset](/images/thumbnails/gallery/lake-sunset.jpg)](https://drive.google.com/file/d/REPLACE_WITH_FILE_ID/view)
*Click the image to view full resolution on Google Drive.*

### Clean-Up Drive 2025

[![Clean-up drive](/images/thumbnails/gallery/clean-up-drive.jpg)](https://drive.google.com/file/d/REPLACE_WITH_FILE_ID/view)
*Click the image to view full resolution on Google Drive.*
```

**Step 16: Create News Pages**

Create `content/news/_index.md`:

```markdown
---
title: "News & Events"
---

Stay updated with the latest news and upcoming events at Klake Lake.
```

Create `content/news/first-post.md`:

```markdown
---
title: "Welcome to Klake Lake Portal"
date: 2026-06-13
---

Welcome to the new Klake Lake website! This portal will serve as a central hub
for all information related to the lake — legal records, contacts, media,
and updates.

Stay tuned for more content coming soon.
```

### Phase 6: Set Up GitHub Actions (Auto-Deploy)

**Step 17: Create the Deploy Workflow**

Create `.github/workflows/hugo-deploy.yml`:

```yaml
name: Deploy Hugo Site to GitHub Pages

on:
  push:
    branches:
      - main  # Runs whenever someone pushes to main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      # Step 1: Get the latest code from the repo
      - name: Checkout repository
        uses: actions/checkout@v4
        with:
          submodules: true

      # Step 2: Install Hugo
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'

      # Step 3: Build the static site
      - name: Build
        run: hugo --minify

      # Step 4: Deploy to GitHub Pages
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

### Phase 7: First Build and Deploy

**Step 18: Commit and Push Everything**

If using command line:
```bash
git add .
git commit -m "Initial site setup with Hugo"
git push origin main
```

If using GitHub web interface, create each file individually using **"Add file" → "Create new file"** and commit each one.

**Step 19: Watch the Build**

1. Go to your repo on GitHub
2. Click the **"Actions"** tab
3. You'll see a workflow running called "Deploy Hugo Site to GitHub Pages"
4. Wait for it to finish (green checkmark) — about 30-60 seconds
5. Click into the workflow run to see build logs if something goes wrong

**Step 20: Visit Your Live Site**

1. Go to **Settings** → **Pages**
2. You'll see a message: "Your site is published at `https://your-org.github.io/klake-portal/`"
3. Click the URL to visit your live website!

### Phase 8: Custom Domain (Optional)

If you want your site at a custom domain like `www.klakelake.org`:

1. Buy a domain from a registrar (GoDaddy, Namecheap, Google Domains — ~$10-15/year)
2. In your GitHub repo: **Settings** → **Pages** → enter your custom domain
3. Follow GitHub's instructions to add DNS records at your domain registrar
4. Enable **"Enforce HTTPS"** (wait a few minutes after setting the domain)

---

## 8. Adding Content (Non-Tech Workflow)

This section describes the exact steps for non-technical people to add content to the website.

### Adding a News Post

**What you need:** Just text (no software needed, just a web browser).

1. Go to `https://github.com/your-org/klake-portal`
2. Navigate to the `content/news/` folder
3. Click **"Add file"** → **"Create new file"**
4. In the filename field, type: `my-post-title.md` (use short, descriptive names with hyphens)
5. Copy and paste this template into the editor:

```markdown
---
title: "Your Post Title"
date: 2026-06-13
---

Write your content here. Use **double asterisks** for bold.
Use single *asterisks* for italic.

## Subheading

Paragraph text goes here.

- Bullet point 1
- Bullet point 2
```

6. Replace the title and date with your actual information
7. Write your content in plain text below the `---` markers
8. Scroll down, add a short description (e.g., "Added June 2026 news post")
9. Select **"Commit directly to the main branch"**
10. Click **"Commit new file"**
11. Wait ~30 seconds, then refresh your website — the new post will appear

**Formatting tips:**
- `# Heading` — large heading
- `## Subheading` — medium heading
- `**bold text**` — bold (use two asterisks)
- `*italic text*` — italic (use one asterisk)
- `[link text](https://example.com)` — hyperlink
- `- item` — bullet point (hyphen then space)

### Adding Photos to the Gallery

**What you need:** The photo file and a web browser.

**Step 1: Upload master copy to Google Drive**

1. Go to `https://drive.google.com`
2. Navigate to `Klake Lake Portal → Photos → Gallery`
3. Click **"New"** → **"File upload"** (or drag and drop)
4. Select your high-resolution photo
5. Wait for upload to complete
6. Right-click the uploaded file → **"Share"** → **"Anyone with the link"** → **"Copy link"**
7. Save this link somewhere (you'll need it in Step 3)

**Step 2: Create and upload thumbnail to GitHub**

A thumbnail is a smaller version of the photo optimized for web. You can create one in two ways:

**Easy method (use a free online tool):**
1. Go to a site like `https://imageresizer.com`
2. Upload your photo
3. Set width to **400 pixels**
4. Download the resized image (save as `descriptive-name.jpg`)
5. Rename the file to something short: e.g., `lake-sunset.jpg`

**Upload to GitHub:**
1. Go to `https://github.com/your-org/klake-portal`
2. Navigate to `static/images/thumbnails/gallery/`
3. Click **"Add file"** → **"Upload files"**
4. Drag your thumbnail file into the upload area
5. Scroll down, add a description like "Added lake-sunset thumbnail"
6. Click **"Commit changes"**

**Step 3: Link the thumbnail and master copy in the gallery page**

1. Go back to GitHub repo → `content/media/gallery.md`
2. Click the pencil icon (✏️) to edit the file
3. Find the gallery entries section and add a new entry:

```markdown
### Your Photo Title

[![Description](/images/thumbnails/gallery/your-photo.jpg)](https://drive.google.com/file/d/REPLACE_WITH_FILE_ID/view)
*Click the image to view full resolution on Google Drive.*
```

4. Replace:
   - `Your Photo Title` — caption/name of the photo
   - `your-photo.jpg` — the exact filename you uploaded as thumbnail
   - `REPLACE_WITH_FILE_ID` — the file ID from your Google Drive link
   - `Description` — brief alt text (for accessibility)
5. Scroll down, add a description like "Added lake sunset photo"
6. Click **"Commit changes"**

That's it! The site will auto-build and the new photo will appear in the gallery.

### Adding a Legal Document

**What you need:** The PDF file and a web browser.

**Step 1: Upload to Google Drive**

1. Go to `https://drive.google.com`
2. Navigate to `Klake Lake Portal → Legal Documents → [appropriate subfolder]`
3. Click **"New"** → **"File upload"**
4. Select your PDF
5. Right-click → **"Share"** → **"Anyone with the link"** → **"Copy link"**

**Step 2: Link from the website**

1. Go to GitHub repo → `content/legal/records.md`
2. Click the pencil icon (✏️) to edit
3. Add a new entry under the appropriate section:

```markdown
- [Document Title (PDF)](https://drive.google.com/file/d/FILE_ID/view)
```

4. Commit the change
5. Site auto-updates within 30 seconds

### Updating Staff Contacts

1. Go to GitHub repo → `content/contacts/staff.md`
2. Click the pencil icon (✏️)
3. Edit the table rows
4. Commit changes

Tables use this format:
```
| Role | Name | Phone | Email |
|------|------|-------|-------|
| Engineer | John Doe | 123-456-7890 | john@example.com |
```
Each `|` separates a column. The second row (`---|---|---|`) is required for formatting.

---

## 9. Backup Strategy

### The 3-2-1 Backup Rule

A proper backup strategy follows the **3-2-1 rule**:
- **3** copies of your data
- **2** different storage types
- **1** copy off-site

### Our Backup Architecture

```
┌──────────────────────────────────────────────────────┐
│                    COPY 1 (Primary)                    │
│                   Google Drive                         │
│  Stores: All master photos, legal PDFs, media files   │
│  Capacity: 15GB free                                   │
│  Backed up by: Google automatically                    │
│  Restore: Download from Drive                          │
└──────────────────────────────────────────────────────┘
                        │
            (Google handles infrastructure backup)
                        │
┌──────────────────────────────────────────────────────┐
│                    COPY 2 (Secondary)                   │
│                   GitHub Repository                     │
│  Stores: All code, markdown content, thumbnails, CSS   │
│  Capacity: Limited by GitHub (generous for text/images)│
│  Backed up by: GitHub automatically                    │
│  Restore: Clone or download ZIP from GitHub            │
└──────────────────────────────────────────────────────┘
                        │
            (GitHub handles infrastructure backup)
                        │
┌──────────────────────────────────────────────────────┐
│              COPY 3 (Off-Site / Physical)               │
│               Local External Drive                      │
│  Stores: Complete backup of everything                  │
│  Frequency: Every 3 months (quarterly)                  │
│  Method: Manual download from Google Drive + GitHub     │
│  Restore: Copy from external drive                     │
└──────────────────────────────────────────────────────┘
```

### What to back up and how often

| What | Where | Frequency | Method |
|------|-------|-----------|--------|
| Full Google Drive folder | External drive | **Every 3 months** | Google Takeout or manual ZIP download |
| GitHub repo | External drive | **Every 3 months** | Download ZIP from GitHub |
| Entire site content | Google Drive + GitHub | **Already backed up by Google/GitHub** | Automatic |

### Step-by-Step: Quarterly Backup (For Non-Tech Users)

**Every 3 months** (set a calendar reminder), do this:

**Part A: Back up Google Drive**

1. Go to `https://takeout.google.com`
2. Sign in with your Klake Lake Google account
3. Click **"Deselect all"**
4. Scroll down and check only **"Google Drive"**
5. Click **"All Drive data included"** → check only `Klake Lake Portal` folder
6. Click **"Next step"**
7. Choose:
   - **Delivery method:** "Send download link via email"
   - **Frequency:** "Export once"
   - **File type:** `.zip`
   - **Size:** 2GB (if larger, it'll be split into multiple files)
8. Click **"Create export"**
9. Wait for email (can take minutes to hours depending on size)
10. Download the ZIP file(s) to your **external drive** in a folder called `Klake-Backup-YYYY-MM-DD/`

**Alternative: Manual download (for smaller folders):**

1. Go to Google Drive → `Klake Lake Portal` folder
2. Right-click the folder → **"Download"**
3. Google Drive will create a ZIP and download it
4. Save to external drive

**Part B: Back up GitHub repo**

1. Go to `https://github.com/your-org/klake-portal`
2. Click the green **"Code"** button
3. Click **"Download ZIP"**
4. Save to external drive as `klake-repo-YYYY-MM-DD.zip`

**Part C: Store the external drive safely**

1. Label the drive: "Klake Lake Backup — Last updated: [date]"
2. Store it in a safe place (different building from your computer if possible)
3. That's it — you're backed up!

### Recovery: How to Restore from Backup

**Scenario 1: Google Drive files accidentally deleted**

1. Check Google Drive **Trash** (files stay for 30 days)
2. Right-click → **"Restore"**
3. If past 30 days, go to your external drive → copy files back to Google Drive

**Scenario 2: GitHub repo corrupted or deleted**

1. Go to your external drive → find the latest `klake-repo-YYYY-MM-DD.zip`
2. Extract the ZIP
3. Go to `https://github.com/new` → create a new repo with the same name
4. Follow GitHub's instructions to upload the extracted files
5. Re-enable GitHub Pages in Settings
6. The site will rebuild automatically

**Scenario 3: Everything lost (fire, flood, theft)**

1. Buy a new external drive
2. Restore from any remaining backup copy (check if a team member has one)
3. Set up everything from scratch using this REPORT.md guide, but with the advantage of having the content ready

### Why This Is Enough

- **Google Drive** keeps version history for 30 days (180 days with Workspace)
- **GitHub** keeps every version of every file forever (it's a version control system)
- **External drive** protects against account loss or service shutdown
- All three services would need to fail simultaneously to lose data — extremely unlikely

### Optional: Automated Backup (for Tech Contributors)

If you want to automate the quarterly reminder:

1. Create a `.github/workflows/backup.yml` file with a cron job that runs on the first of every month
2. This GitHub Action would send a reminder email to the project's Google account
3. It uses the **schedule** trigger:

```yaml
name: Backup Reminder
on:
  schedule:
    - cron: '0 0 1 */3 *'  # Runs at midnight on the 1st of every 3rd month
jobs:
  remind:
    runs-on: ubuntu-latest
    steps:
      - name: Send reminder
        run: |
          echo "============================================"
          echo "BACKUP REMINDER"
          echo "============================================"
          echo "It's time to download backups from:"
          echo "1. Google Drive (takeout.google.com)"
          echo "2. GitHub (download ZIP from repo)"
          echo ""
          echo "Save to your external drive."
          echo "============================================"
```

---

## 10. Maintenance Guide

### Quick Reference Card (For Non-Tech People)

Print this page or bookmark it.

| Task | What to Do | Takes |
|------|-----------|-------|
| **Add news post** | GitHub → `content/news/` → "Add file" → paste template → commit | 5 minutes |
| **Add gallery photo** | (1) Upload to Google Drive → (2) Resize thumbnail → (3) Upload thumbnail to GitHub `static/images/thumbnails/gallery/` → (4) Edit `content/media/gallery.md` to link it | 10 minutes |
| **Add legal PDF** | (1) Upload to Google Drive → (2) Edit `content/legal/records.md` → paste link → commit | 5 minutes |
| **Update staff contacts** | Edit `content/contacts/staff.md` → update table → commit | 2 minutes |
| **Change navbar link** | Ask a tech person | — |
| **Change site design** | Ask a tech person | — |

### How to know if the site is working

1. Visit `https://your-org.github.io/klake-portal/`
2. If it loads, it's working
3. After making changes, wait 30-60 seconds and refresh
4. If changes don't appear after 2 minutes, check the **"Actions"** tab on GitHub — if the workflow has a red ✗, click into it to see the error

### What NOT to Do

- Do not delete or rename files in `layouts/` unless you know what you're doing
- Do not change `config.toml` unless you know what you're doing
- Do not rename the `content/` folder or its subfolders (this breaks the navigation)
- Do not delete the `.github/workflows/` folder (this breaks auto-deploy)

### For Tech Contributors: Making Major Changes

**Adding a new section:**
1. Create a new folder in `content/`, e.g., `content/projects/`
2. Create `content/projects/_index.md` with `title: "Projects"`
3. Add a new entry in `config.toml` under `[[menu.main]]`
4. Create individual pages in the folder

**Changing the theme:**
1. Edit files in `layouts/` — these are HTML templates
2. Edit `static/css/style.css` — site-wide styles
3. Test locally with `hugo server` before pushing

**Installing Hugo locally (for testing):**

On Linux/Mac:
```bash
# Download Hugo
wget https://github.com/gohugoio/hugo/releases/download/v0.126.0/hugo_extended_0.126.0_linux-amd64.tar.gz
# Extract and install
tar -xzf hugo_*.tar.gz
sudo mv hugo /usr/local/bin/
# Verify
hugo version
```

On Windows: Download the `.exe` from Hugo's GitHub releases page.

Then to test the site locally:
```bash
cd klake-portal
hugo server
# Open http://localhost:1313 in your browser
```

---

## 11. Cost Breakdown

### Annual Operating Costs: $0

| Service | Cost | What It Provides |
|---------|------|-----------------|
| GitHub (free plan) | $0 | Unlimited public repos, 1GB storage, 2000 CI minutes/month |
| GitHub Actions | $0 | Included in free plan (2000 min/month — our build takes ~30s) |
| GitHub Pages | $0 | Static hosting, HTTPS, custom domain support |
| Google Drive (free) | $0 | 15GB storage for master files |
| YouTube | $0 | Video hosting |
| Domain (optional) | ~$10-15/year | Custom domain like `klakelake.org` |
| External drive (one-time) | ~$20-50 | Physical backup |

**Without custom domain: $0/year**
**With custom domain: ~$10-15/year**

### Comparison with Alternatives

| Option | Monthly Cost | Yearly Cost | Why Not |
|--------|-------------|-------------|---------|
| **This solution** | $0 | $0 | — |
| WordPress.com (free) | $0 | $0 | Limited features, ads, no custom domain |
| WordPress.com (premium) | $8/mo | $96/yr | Can't embed Google Drive easily |
| Wix/Squarespace | $16/mo | $192/yr | Vendor lock-in, ongoing cost |
| Traditional hosting + CMS | $10/mo | $120/yr | Server maintenance, security updates |

---

## 12. Glossary

Terms are ordered from most to least important to understand.

| Term | Meaning | Analogy |
|------|---------|---------|
| **Repository (repo)** | A folder where all project files live, with full version history | A Google Drive folder that remembers every version ever |
| **Git** | The system that tracks changes to files | Like "Track Changes" in Word, but for all files |
| **GitHub** | A website that hosts Git repositories online | Google Drive for code, but with change tracking |
| **Markdown** | A simple way to format text using plain symbols (like `**bold**`) | Writing an email with simple formatting |
| **Static site** | A website where pages are pre-built HTML files | A printed book vs a live news ticker |
| **Static site generator** | A tool that converts markdown files into HTML pages | A printing press for the book |
| **Hugo** | The specific static site generator we use | Our specific printing press model |
| **GitHub Pages** | A free service that hosts your website from a GitHub repo | A free shelf to put your book on |
| **GitHub Actions** | Automated tasks that run when code changes | A robot that builds and ships your book every time you edit it |
| **Deploy** | To publish the built website to the live server | Moving the book from the press to the shelf |
| **Build** | The process of converting markdown into HTML | Printing the book |
| **Commit** | Saving a snapshot of changes to the repository | Saving a version with a note saying what changed |
| **Push** | Sending your commits to GitHub | Uploading your saved version to the cloud |
| **Branch** | A separate line of development | A draft copy vs the published version |
| **Main branch** | The primary branch — what the live site is built from | The final published version |
| **Clone** | Downloading a repo to your computer | Making a local copy of the Google Drive folder |
| **Theme** | A set of templates that define the look of the site | The book's layout and design |
| **Template** | An HTML file with placeholders where content is inserted | A book page template with blanks for content |
| **Content** | The text, images, and files that make up the site | The actual words and pictures in the book |
| **Front matter** | Metadata at the top of a markdown file (title, date, etc.) | The book's catalog entry (title, author, date) |
| **URL** | The web address of a page | The page number in a book |
| **HTTPS** | Encrypted connection to the website | A secure, private phone line |
| **DNS** | The system that maps domain names to server addresses | A phonebook for websites |
| **3-2-1 backup** | 3 copies, 2 media types, 1 off-site | Three safes in two different buildings, one far away |
| **CI/CD** | Continuous Integration/Continuous Deployment — auto-build and auto-deploy | An assembly line that builds and ships automatically |
| **Cloud** | Someone else's computer running somewhere else | Renting storage/servers instead of owning them |
| **SSG** | Static Site Generator (same as above) | — |

---

## Appendix A: File Reference

### Quick File Location Reference

| Content Type | Storage Location (Master) | Display Location (Site) |
|-------------|-------------------------|----------------------|
| Homepage text | `content/_index.md` | `/` |
| About page text | `content/about.md` | `/about/` |
| Legal records list | `content/legal/records.md` | `/legal/records/` |
| Legal developments | `content/legal/developments.md` | `/legal/developments/` |
| Staff contacts | `content/contacts/staff.md` | `/contacts/staff/` |
| Volunteer emails | `content/contacts/volunteers.md` | `/contacts/volunteers/` |
| Media coverage | `content/media/coverage.md` | `/media/coverage/` |
| Gallery page | `content/media/gallery.md` | `/media/gallery/` |
| News post | `content/news/post-name.md` | `/news/post-name/` |
| Gallery thumbnails | `static/images/thumbnails/gallery/` | `/images/thumbnails/gallery/` |
| News thumbnails | `static/images/thumbnails/news/` | `/images/thumbnails/news/` |
| Small PDFs | `static/documents/` | `/documents/` |
| Google Drive master | Google Drive `Klake Lake Portal/` | Linked via URL |

### File Naming Rules

- Use lowercase letters, numbers, and hyphens only
- No spaces in filenames (use `-` instead)
- No special characters (`@`, `#`, `$`, `%`, `&`, etc.)
- Keep filenames short but descriptive
- Examples: `lake-sunset.jpg`, `water-rights-1998.pdf`, `clean-up-drive-2025.md`

### Markdown Front Matter Template

Every content file starts with `---` lines:

```markdown
---
title: "Page Title"
date: 2026-06-13
draft: false
---
```

- `title` — appears as the page heading and in browser tab
- `date` — for news posts, controls ordering (newest first)
- `draft` — set to `true` to hide a page without deleting it

---

## Appendix B: Troubleshooting

### Site not updating after changes

1. Wait 60 seconds (GitHub Actions takes time to run)
2. Hard refresh your browser (Ctrl+F5 or Cmd+Shift+R)
3. Go to GitHub → **"Actions"** tab — is the workflow running?
4. If the workflow has a red ✗, click into it to see the error
5. Common errors:
   - Missing comma or quote in `config.toml`
   - Incorrect front matter in a markdown file
   - File name with spaces or special characters

### Google Drive link not working

1. Make sure the file is shared with **"Anyone with the link"**
2. Copy the link again (right-click → **"Share"** → **"Copy link"**)
3. Check that the link in the markdown file is exactly the same

### Can't upload to GitHub

1. Make sure you're logged into GitHub
2. Make sure you have write access to the repository
3. Files must be under 100MB each
4. Use lowercase filenames with hyphens

---

## Summary

You now have everything needed to build, maintain, and backup a free website for Klake Lake. The total cost is **$0/year** (excluding optional custom domain).

| Task | Who Does It | How Often |
|------|-----------|-----------|
| Add news/events | Non-tech | As needed |
| Add photos | Non-tech | As needed |
| Add legal docs | Non-tech | As needed |
| Update contacts | Non-tech | As needed |
| Quarterly backup | Non-tech | Every 3 months |
| Theme/design changes | Tech | Rarely |
| New sections | Tech | Rarely |
| Check site is live | Anyone | Daily (optional) |

---

*This report was generated as part of the Klake Lake Portal project. Last updated: June 2026.*
