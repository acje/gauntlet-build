# Theme Overrides

Local layout overrides for the Blowfish theme (v2.x, as of 2026-04). Each file shadows its counterpart under `themes/blowfish/layouts/`. Review against upstream on theme upgrades.

## Overrides

### `layouts/partials/header/basic.html`

**Upstream:** `themes/blowfish/layouts/partials/header/basic.html`

**Purpose:** Hide the text site-title link on the homepage while keeping default behavior on other pages. Dead subnavigation and highlight-menu blocks removed (not configured).

### `layouts/partials/home/background.html`

**Upstream:** `themes/blowfish/layouts/partials/home/background.html`

**Purpose:** Render homepage H1 from `.Site.Title` instead of `params.author.name`. Suppress homepage author headline and social links. Remove light-mode white background overlay while preserving dark-mode darkening. Left-align content instead of upstream's centered layout. The secondary overlay is controlled by `assets/css/custom.css` — review both together.

### `layouts/shortcodes/list.html`

**Upstream:** `themes/blowfish/layouts/shortcodes/list.html`

**Purpose:** Adds `orderByWeight=true` to sort by front matter weight before limiting. Excludes the current page before applying limit — upstream excludes after limiting, which can return fewer than `limit` pages; this override guarantees the full `limit` count. On the homepage, `cardView` eagerly loads only the first two card images for LCP.

### `layouts/partials/article-link/card.html`

**Upstream:** `themes/blowfish/layouts/partials/article-link/card.html`

**Purpose:** Adds `style="grid-column: span N"` via `.Params.span` on `<article>` for variable-width cards. Supports dict context for per-render image loading control. Adds explicit decorative-image alt text and removes trailing spacer.

### `layouts/partials/favicons.html`

**Upstream:** `themes/blowfish/layouts/partials/favicons.html`

**Purpose:** Self-managed favicon stack. Generates web manifest from site config. Favicon assets live in `/static`. Theme/background color `#0a0a0f` is hardcoded here and in `assets/css/custom.css`.

### `layouts/partials/extend-footer.html`

**Upstream:** `themes/blowfish/layouts/partials/extend-footer.html`

**Purpose:** Intentionally empty. Prevents theme or example overrides from injecting custom footer scripts.

## Upgrade Checklist

When upgrading the Blowfish theme:

1. Diff each file above against its new upstream counterpart.
2. Pay special attention to `home/background.html` and `assets/css/custom.css` (coupled).
3. Check if upstream adds features that make any override unnecessary.
