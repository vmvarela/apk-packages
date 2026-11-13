# apk-packages

APK repository for [vmvarela](https://github.com/vmvarela) tools (Alpine Linux), served via GitHub Pages.

The site is built by the shared publisher in [package-distribution](https://github.com/vmvarela/package-distribution) (`scripts/apk-publish.mjs`), pinned to a commit, so every destination uses the same catalog and the same durable-state rules. `state/` in this repository is that state: `state/<package>/state.json` plus `pool/` with the exact release bytes.

## Installation

```bash
wget -qO /etc/apk/keys/vmvarela.rsa.pub \
  https://vmvarela.github.io/apk-packages/vmvarela.rsa.pub
echo "https://vmvarela.github.io/apk-packages" >> /etc/apk/repositories
apk update && apk add sql-pipe
```

No `--allow-untrusted`: the index is signed, and Alpine trusts the packages through it.

## Packages

| Package | Description |
|---------|-------------|
| [sql-pipe](https://github.com/vmvarela/sql-pipe) | Read CSV from stdin, query with SQL, write CSV to stdout |
| [damas](https://github.com/vmvarela/damas) | Draughts engine and apps (English draughts and Spanish damas) |

## How it is built

`.github/workflows/update.yml` runs on `repository_dispatch` (`update-sql-pipe`, `update-damas`) or manually with a package and version. It checks out `package-distribution` at `PACKAGE_DISTRIBUTION_SHA`, resolves the target and version, and runs the publisher inside `alpine:latest` (apk-tools and abuild are Alpine-only). It deploys to Pages only when the state changes, then commits the candidate state back here.

Archived projects are not published here: `catalog/ghoten.json` has no APK channel.

## Required Secrets

| Secret | Description |
|--------|-------------|
| `APK_SIGNING_KEY` | RSA private key (PEM) for signing APKINDEX |
| `APK_SIGNING_KEY_PUB` | Matching public key (PEM), served as `vmvarela.rsa.pub` |

The workflow refuses to publish without both, and refuses a private key whose derived public key does not match `APK_SIGNING_KEY_PUB`; rotating it would break every client that already installed the old key.

Generate a new pair with:

```bash
openssl genrsa -out vmvarela.rsa 2048
openssl rsa -in vmvarela.rsa -pubout > vmvarela.rsa.pub
```
