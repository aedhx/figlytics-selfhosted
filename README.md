# Figlytics — self-hosted

Analytics for Figma design systems — component adoption, tokens and styles, team activity, health scores and Figma AI spend — running on your own infrastructure.

<img width="1200" alt="Figlytics dashboard" src="https://figlytics.adx.cool/Figlytics.png" />

This repository holds the documentation, the compose file and the releases of the **Docker image**. The source code is not public. Product site: [figlytics.adx.cool](https://figlytics.adx.cool).

## Quick start

Requires Docker.

```bash
curl -O https://raw.githubusercontent.com/aedhx/figlytics-selfhosted/main/docker-compose.yml
docker compose up -d
```

Open [http://localhost:3000](http://localhost:3000) and follow the setup wizard: paste a Figma personal access token, then pick your libraries and team.

## Figma requirements

Most pages rely on the Figma Library Analytics API (`library_analytics:read`), available on the **Figma Enterprise** plan. Leaderboard, Comments, Files and Settings work on lower plans. Settings → Diagnostics tells you exactly which permissions your token has and what each missing one costs. Details: [figlytics.adx.cool](https://figlytics.adx.cool/#faq).

## Configuration

| Variable | Default | Purpose |
|---|---|---|
| `FIGLYTICS_PASSWORD` | unset | Instance password. Unset: no login. Set: every page and API requires signing in. |
| `FIGLYTICS_DATA_DIR` | `/data` | Where configuration, cache and saved reports are stored. Keep it on a volume. |
| `PORT` | `3000` | Port the server listens on inside the container. |

## Sharing it with your team

Without a password, anyone who can reach the port can read your Figma data — which is why the compose file only publishes it on `127.0.0.1`.

To share the dashboard:

1. set `FIGLYTICS_PASSWORD` to a long password;
2. publish the port on your network (`"3000:3000"`);
3. put it behind a reverse proxy with **HTTPS** (Caddy, Traefik, nginx…) so the session cookie is sent securely. The proxy must forward `X-Forwarded-Proto`.

Changing the password signs everyone out. Sessions last 30 days.

## Updating

```bash
docker compose pull
docker compose up -d
```

`ghcr.io/aedhx/figlytics:1` follows the latest 1.x release. Pin an exact version (`:1.4.0`) if you prefer to update deliberately. Release notes are on the [Releases](https://github.com/aedhx/figlytics-selfhosted/releases) page.

## Backups

Everything Figlytics writes lives in the `figlytics-data` volume. Back up that volume; nothing else is needed.

## Privacy

The image talks to the Figma API and nothing else. It contains no telemetry and no analytics.

## Troubleshooting

- **The page asks for a password I never set** — another `FIGLYTICS_PASSWORD` is set in your environment or compose file.
- **I can't sign in behind my proxy** — check that the proxy forwards `X-Forwarded-Proto` and serves HTTPS.
- **A page says a credential is missing** — open Settings → Diagnostics.

## Support

Bugs and feature requests: [open an issue](https://github.com/aedhx/figlytics-selfhosted/issues/new/choose). Security issues: use **Report a vulnerability** in the Security tab, not a public issue.

## License

Free to use, including commercially, within your organisation. No redistribution, resale or hosting for third parties. See [LICENSE](LICENSE).
