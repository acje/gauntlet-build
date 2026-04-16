# gauntlet.build — Deployment Plan

## Overview

Hugo site with Blowfish theme showcasing three projects (Quicksilver, Pardosa, Gauntlet), deployed via GitHub Actions to GitHub Pages at `gauntlet.build`. Mirrors `acje.github.io` infrastructure pattern.

## Projects

| Project | Description | Repo |
|---------|-------------|------|
| **Quicksilver** | EDA components in Rust. Reusable crates (`quics-*`) for web serving, memoization, and API collection in event-driven architectures. | [acje/quicksilver](https://github.com/acje/quicksilver) |
| **Pardosa** | Event-driven data layer on NATS/Jetstream implementing fiber semantics. Ordered event storage with fibers, lines, and migration support. | [acje/fiber-semantics](https://github.com/acje/fiber-semantics) |
| **Gauntlet** | Actor Enclave Model (AEM). Security-oriented actor model specification for distributed computing with WASM-sandboxed enclaves, capability-based messaging, and supervision hierarchies. | [acje/gauntlet](https://github.com/acje/gauntlet) |

## Architecture

Two independent GitHub Pages sites. No shared state.

| Site | Repo | Domain | Theme |
|------|------|--------|-------|
| `acje.github.io` | `acje/acje.github.io` | `acje.github.io` (no custom domain) | hugo-book |
| `gauntlet.build` | `acje/gauntlet-build` | `gauntlet.build` (custom domain) | blowfish |

## Steps

### 1. Init repo and add theme

Complete steps 2–5 first, then create the GitHub repo and push.

```bash
git init
```

### 2. Add Blowfish theme as git submodule

Pin to a release tag, not `main`, for supply-chain safety. The `-b` flag ensures `.gitmodules` tracks the tag, not `main`.

```bash
git submodule add -b <latest-release-tag> https://github.com/nunocoracao/blowfish themes/blowfish
# e.g. git submodule add -b v2.87.6 https://github.com/nunocoracao/blowfish themes/blowfish
```

### 3. Create GitHub repo

After creating all files (steps 4–7), commit and push:

```bash
git add . && git commit -m "feat: initial Hugo site with Blowfish theme"
gh repo create acje/gauntlet-build --public --source=. --push
```

### 4. Create Hugo config

Directory structure: `config/_default/`

| File | Key settings |
|------|-------------|
| `hugo.toml` | `baseURL = "https://gauntlet.build/"`, `theme = "blowfish"` |
| `params.toml` | Homepage layout, color scheme, author metadata |
| `languages.en.toml` | Site title, author info |
| `menus.en.toml` | Nav: Home, Projects |

### 5. Create content

| File | Purpose |
|------|---------|
| `content/_index.md` | Homepage |
| `content/projects/_index.md` | Project listing page |
| `content/projects/quicksilver.md` | Quicksilver project page |
| `content/projects/pardosa.md` | Pardosa project page (links to fiber-semantics repo) |
| `content/projects/gauntlet.md` | Gauntlet project page |

### 6. Create supporting files

| File | Purpose |
|------|---------|
| `static/CNAME` | Contains `gauntlet.build` — required for Actions-based deploys (placed in `public/` at build time). The repo Settings → Pages custom domain setting alone does not persist across Actions deploys. |
| `.gitignore` | `public/`, `resources/`, `.hugo_build.lock` |

### 7. Create GitHub Actions workflow

File: `.github/workflows/hugo.yaml`

Adapted from `acje/acje.github.io` workflow:

- Hugo 0.156.0, Go 1.26.0, Dart Sass 1.97.3
- **No Node.js** — Blowfish ships pre-built CSS
- `submodules: recursive` in checkout action
- Permissions: `contents: read`, `pages: write`, `id-token: write`
- Pages source: **GitHub Actions** (not branch-based deploy)

### 8. Verify domain in GitHub

1. Go to GitHub account **Settings → Pages → Verified domains**
2. Add `gauntlet.build`
3. Add the TXT record `_github-pages-challenge-acje` to Cloudflare DNS
4. Complete verification

### 9. Configure Cloudflare DNS

All records use **DNS-only (grey cloud)** — required so GitHub can provision TLS certificates via Let's Encrypt.

| Type | Name | Target | Proxy |
|------|------|--------|-------|
| CNAME | `@` | `acje.github.io` | DNS-only (grey cloud) |
| CNAME | `www` | `acje.github.io` | DNS-only (grey cloud) |

Cloudflare CNAME flattening handles the apex domain automatically (works in DNS-only mode). **Note:** If DNS ever moves off Cloudflare, replace apex CNAME with four A records (`185.199.108-111.153`) plus AAAA records per [GitHub docs](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site).

**Do not** enable orange cloud (proxy) — GitHub cannot issue Let's Encrypt certificates when Cloudflare intercepts the ACME challenge.

### 10. Enable GitHub Pages

1. Repo **Settings → Pages**
2. Source: **GitHub Actions**
3. Custom domain: `gauntlet.build`
4. Enforce HTTPS: ✅

### 11. Verify

```bash
git push -u origin main
```

Wait ~15 minutes for certificate provisioning, then verify:

```bash
# DNS resolves to GitHub Pages IPs (185.199.108-111.153)
dig gauntlet.build +short

# Valid TLS certificate, HTTP 200
curl -vI https://gauntlet.build

# HTTP redirects to HTTPS
curl -I http://gauntlet.build

# www subdomain works
curl -I https://www.gauntlet.build

# Existing site still functional
curl -vI https://acje.github.io
```

## Troubleshooting

### TLS certificate not provisioning

- Verify Cloudflare proxy is **off** (grey cloud)
- Verify domain is verified in GitHub account settings
- Check GitHub Pages settings for error messages
- Wait up to 1 hour; GitHub rate-limits cert provisioning

### Site returns 404

- Verify `static/CNAME` contains exactly `gauntlet.build` (no protocol, no trailing slash)
- Verify Pages source is set to "GitHub Actions" not "Deploy from branch"
- Check Actions workflow completed successfully

### Existing site (acje.github.io) broken

- These are independent deployments — no shared config
- If broken, check `acje/acje.github.io` repo Settings → Pages — custom domain should be empty

### Rollback

If deployment fails partway:

1. **DNS:** Remove CNAME records for `gauntlet.build` in Cloudflare → domain stops resolving (safe)
2. **GitHub Pages:** Repo Settings → Pages → disable GitHub Pages
3. **Repo:** Delete via `gh repo delete acje/gauntlet-build --yes` if starting over
