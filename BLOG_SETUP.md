# Blog Setup for Danny Shmueli's Hexo Blog

## Overview

This repository hosts the source of [dannyshmueli.com](https://dannyshmueli.com) – a static web-site generated with **Hexo** and the beautiful **Fluid** theme.  Hexo transforms all content found in the `source/` directory into static HTML files under `public/`, which can then be served from any web-server or a CDN such as GitHub Pages.

* **Static-site engine:** Hexo `^7.3.0`
* **Theme:** [hexo-theme-fluid](https://github.com/fluid-dev/hexo-theme-fluid) `^1.9.8`
* **Node project name:** `hexo-site`

---

## Prerequisites

1. **Node.js** ≥ 18 (Hexo requires Node 14+, but the latest LTS is recommended).
2. **npm** (ships with Node) – all runtime & build dependencies are declared in `package.json`.

> The `hexo` CLI is installed locally via `devDependencies` and exposed through npm scripts – no global install is needed.

---

## Getting Started

```bash
# 1. Clone the repository
$ git clone https://github.com/dannyshmueli/dannyshmueli.github.io-new.git
$ cd dannyshmueli.github.io-new

# 2. Install dependencies (exact versions locked via package-lock.json)
$ npm ci        # or: npm install

# 3. Start a local dev server ( <http://localhost:4000> )
$ npm run server
```

While the dev-server is running Hexo watches the filesystem and rebuilds pages on change.  Theme changes reload automatically as well.

---

## Useful npm Scripts / Hexo Commands

| Task                | npm script        | Under the hood               |
|---------------------|-------------------|------------------------------|
| Add post            | `npm run add-post`| `hexo new post`                 |
| Clean caches/output | `npm run clean`   | `hexo clean`                 |
| Local dev preview   | `npm run server`  | `hexo server` (defaults to `localhost:4000`) |
| Generate site       | `npm run build`   | `hexo generate` → `public/`  |
| Deploy              | `npm run deploy`* | `hexo deploy`                |

`*` Deployment is wired for **GitHub Pages** via `hexo-deployer-git` – configure your remote/branch in `deploy` section of the root `_config.yml`.

---

## Directory Structure (simplified)

```text
├─ public/          # <-- generated static site (ignored by Git)
├─ source/
│   ├─ _posts/      # Markdown blog posts (year-month-day-title.md)
│   ├─ about/       # Custom pages
│   └─ img/         # Site-wide images referenced by posts & theme
├─ themes/          # Theme sub-modules (Fluid comes from npm but can be customised here)
├─ _config.yml      # Global Hexo configuration
├─ _config.fluid.yml# Theme (Fluid) configuration
├─ package.json     # Node scripts & dependencies
└─ BLOG_SETUP.md    # ← (this file)
```

---

## Theme (Fluid) Highlights

The majority of visual behaviour is controlled via `_config.fluid.yml`.  Key tweaks:

* **Dark-mode toggle** enabled (`dark_mode.enable: true`).
* **Navigation bar** customised under `navbar.menu` – Home, Categories, About.
* **Header/Banner images** – large images configured for home & posts (`/img/banner.jpeg`, etc.).
* **About page** personalised (avatar, social links, intro text).
* **Code-block enhancements** – line numbers, copy button, Highlight.js with GitHub & dark themes.
* **Fun features** – typing subtitle effect, progress-bar, anchor icons on headings.

Feel free to explore and adjust these values to match your brand.

---

## Creating Content

1. **New Post**
   ```bash
   $ npx hexo new post "My Awesome Article"
   # → source/_posts/my-awesome-article.md
   ```
   Edit the front-matter (`title`, `date`, `categories`, `tags`, `index_img`, etc.) and write in Markdown.

2. **New Page**
   ```bash
   $ npx hexo new page about
   # Edit the generated source/about/index.md and add content/banner images.
   ```

3. **Assets** – Place post-specific images inside `source/img/` (or enable `post_asset_folder` if desired).

---

## Building & Publishing

```bash
# Generate static files
$ npm run build

# Option A: Push the contents of public/ to any static host.

# Option B (recommended): GitHub Pages via hexo-deployer-git
$ npm run deploy
```

The deploy script will commit the freshly built site to the branch configured in `_config.yml` (commonly `gh-pages` or the `main` branch of `<username>.github.io`).  Make sure you have push rights and an access token if using CI.

## GitHub Pages deploy workflow (hexo-deployer-git)

The project is wired so that **only the generated static files** are pushed to a dedicated `gh-pages` branch, keeping `master` (source) clean.  Quick reference:

1. One-time setup already done
   * Added `hexo-deployer-git` to `package.json`.
   * `_config.yml` contains:
     ```yaml
     deploy:
       type: git
       repo: git@github.com:dannyshmueli/dannyshmueli.github.io.git
       branch: gh-pages
     ```
   * Initial `gh-pages` branch created with a `.nojekyll` file.
   * GitHub → Settings → Pages is configured to serve **Branch: `gh-pages` / `(root)`**.

2. Publish a new version (local machine)
   ```bash
   npm install          # only needed on new machines / fresh clones
   hexo clean && hexo g # rebuild site into public/
   hexo deploy          # commits & pushes public/ → gh-pages
   ```
   GitHub Pages will redeploy within ~60 seconds.

3. Troubleshooting
   • Verify `public/` contains fresh HTML (`hexo g`).  
   • Ensure you are on `master` when running `hexo deploy`; the plugin handles the branch switch internally.  
   • If deployment fails, check SSH auth (the deployer uses the `repo` URL verbatim).

That's it—**a single `hexo deploy` publishes the blog** while keeping the repository history tidy.

---

## Upgrading Dependencies

```bash
$ npm outdated      # check for newer theme / hexo versions
$ npm update        # upgrade → verify → commit
```

Changing the **Fluid** theme version may introduce breaking changes – review the [release notes](https://github.com/fluid-dev/hexo-theme-fluid/releases) and refresh your custom `theme` config.

---

## Troubleshooting

* **Port already in use** – Change the port with `hexo server -p 5000`.
* **Build errors about plugins** – Ensure that every listed dependency in `package.json` is installed & compatible with your Node version.
* **Missing images after deployment** – Verify that paths are correct and assets are under `source/` (they are copied as-is).

---

Happy blogging! 🎉 