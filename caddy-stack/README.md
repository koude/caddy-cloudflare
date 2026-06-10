# Caddy stack

This stack replaces the current Lucky reverse proxy on Synology.

## Image build

GitHub Actions builds a Caddy image with the Cloudflare DNS plugin and pushes it
to GHCR:

```text
ghcr.io/<github-user-or-org>/caddy-cloudflare:latest
```

The Cloudflare token is not used during image build. It is only needed at
runtime on Synology for DNS-01 certificate issuance.

## Runtime environment

Create `.env` from `.env.example` on Synology:

```env
ACME_EMAIL=you@example.com
CF_API_TOKEN=cloudflare_dns_edit_token
CADDY_IMAGE=ghcr.io/<github-user-or-org>/caddy-cloudflare:latest
```

Cloudflare token permissions:

```text
Zone / Zone / Read
Zone / DNS / Edit
```

Scope the token to `koude.win`.

## Deployment notes

The intended production listener is Synology `192.168.50.3:20443`, matching the
current Lucky setup. The router can keep forwarding public `404` to internal
`20443`.

Low-risk migration:

1. Keep Lucky running.
2. Temporarily change the Caddyfile site address from `:20443` to another
   unused port such as `:21443`.
3. Start Caddy and test from LAN with explicit port `21443`.
4. Stop Caddy.
5. Stop Lucky.
6. Change Caddyfile back to `:20443`.
7. Start Caddy.

Rollback is stopping Caddy and starting Lucky again.
