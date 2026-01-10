# Danny Shmueli's Blog

Personal blog powered by [Hexo](https://hexo.io/) with the [Fluid](https://github.com/fluid-dev/hexo-theme-fluid) theme.

## Quick Reference

### Add a New Post

```bash
npm run add-post "My Post Title"
```

This creates a new file at `source/_posts/my-post-title.md`. Edit the front-matter and write your content in Markdown.

### Deploy to GitHub Pages

```bash
npm run clean && npm run build && npm run deploy
```

Or step by step:
1. `npm run clean` - Clear cached files
2. `npm run build` - Generate static site
3. `npm run deploy` - Push to gh-pages branch

The site will be live at [dannyshmueli.com](https://dannyshmueli.com) within ~60 seconds.

### Local Development

```bash
npm run server
```

Preview at http://localhost:4000 with live reload.

---

See [BLOG_SETUP.md](BLOG_SETUP.md) for detailed documentation.
