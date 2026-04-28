# Shayne's Website
This is my first website I've built for myself that wasn't for a school project. This website will be, more or less, a mixture of blog posts and a page to showcase my skills and information, either for future employers, or for anyone in the world who happens to find me!

The website currently showcases a few things, such as:
- An about me section
- Work experiences regarding Computer Science
- CryptoHack notes

## Deploying with Cloudflare Pages

This site is a static HTML project. Cloudflare Pages serves the root URL (`/`) from `index.html`.

### Recommended Pages settings (no build step)

- Framework preset: `None`
- Build command: (leave blank)
- Build output directory: `.`

If you connect a Git repo and your project lives in a subfolder, also set:

- Root directory: the folder that contains `index.html` and `photos/`

### Custom domain

In Cloudflare Pages:

- Go to your Pages project -> Custom domains -> Add your domain.

Cloudflare will guide you through the DNS records (usually a `CNAME` to your `*.pages.dev` hostname). After DNS propagates, your domain should serve the same content as the `pages.dev` URL.

### Common cause of a blank/404 site

If Pages is configured with a non-existent output directory (e.g. `dist`, `public`), it deploys an empty site. For this repo, the output directory should be `.` unless you later add a build tool.