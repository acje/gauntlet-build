# Security Policy

## Reporting a Vulnerability

To report a security vulnerability, open a [private security advisory](https://github.com/acje/gauntlet-build/security/advisories/new) on this repository. Do not open a public issue.

You should receive an acknowledgment within 72 hours.

## Secret Handling

- Never commit secrets, API keys, certificates, or credentials to this repository.
- `.gitignore` blocks common secret file patterns (`.env*`, `*.pem`, `*.key`, `credentials.json`, etc.).
- CI workflows should not print or expose secrets in logs.
- If you suspect a secret was committed, notify the maintainers immediately so it can be rotated and scrubbed from history.

## Supported Versions

Only the latest deployment from `main` is actively maintained.
