# gauntlet-build

Hugo static site with Blowfish theme.

## Deployment

GitHub Pages deployment is **intentionally disabled** (2026-04-17). The workflow `Deploy Hugo site to Pages` (ID: 261752611) is set to `disabled_manually`.

### Re-enable deployment

```sh
gh api -X PUT repos/acje/gauntlet-build/actions/workflows/261752611/enable
```

### Disable deployment

```sh
gh api -X PUT repos/acje/gauntlet-build/actions/workflows/261752611/disable
```
