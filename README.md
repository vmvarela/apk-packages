# apk-packages

APK repository for [vmvarela](https://github.com/vmvarela) tools (Alpine Linux), served via GitHub Pages.

## Installation

```bash
wget -qO /etc/apk/keys/vmvarela.rsa.pub \
  https://vmvarela.github.io/apk-packages/vmvarela.rsa.pub
echo "https://vmvarela.github.io/apk-packages" >> /etc/apk/repositories
apk update && apk add sql-pipe
```

## Packages

| Package | Description |
|---------|-------------|
| [sql-pipe](https://github.com/vmvarela/sql-pipe) | Read CSV from stdin, query with SQL, write CSV to stdout |

## Required Secrets

| Secret | Description |
|--------|-------------|
| `APK_SIGNING_KEY` | RSA private key (PEM) for signing APKINDEX |
| `APK_SIGNING_KEY_PUB` | RSA public key (PEM) served as `vmvarela.rsa.pub` |

Generate with:
```bash
openssl genrsa -out vmvarela.rsa 2048
openssl rsa -in vmvarela.rsa -pubout > vmvarela.rsa.pub
```

If secrets are not set the APKINDEX is built but left unsigned (install requires `--allow-untrusted`).
