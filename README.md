# HappyX

Daily mood check-ins for Discord servers. Members answer "how are you today?" (1-5 plus an
optional message), and the server admin gets a weekly or monthly report.

| Repo | Status |
| --- | --- |
| [HappyX.Api](https://github.com/moixblau/HappyX.Api) | Go + PostgreSQL REST API, multi-server |
| [HappyX.Bot](https://github.com/moixblau/HappyX.Bot) | Go Discord bot: daily survey by DM, reports to the admin |
| [HappyX.Web](https://github.com/moixblau/HappyX.Web) | Old static prototype; TypeScript web app later |

## Run locally

```sh
git clone --recurse-submodules https://github.com/moixblau/HappyX
cd HappyX
cp .env.example .env   # fill in a password, the Discord token and the bot's API key + its hash
docker compose up -d --build
curl localhost:8083/health
```

See HappyX.Bot's README for creating the Discord application (it needs the Server Members intent).

Secrets live only in `.env`, which is git-ignored. See HappyX.Api's README for how API keys work.

## Deploy with Portainer

The HappyX.Api and HappyX.Bot workflows publish multi-arch images (amd64 and arm64) to GHCR on
every merge to `main` (`latest`, `sha-<commit>`) and on `v*` tags (`1.2.3`).

1. In Portainer, go to **Stacks > Add stack > Repository**, use this repo and the compose path
   `deploy/portainer-stack.yml`. Or paste that file into the web editor.
2. Under **Environment variables**, set `POSTGRES_PASSWORD`, `API_KEY_HASHES`, `DISCORD_TOKEN` and
   `BOT_API_KEY`. Optionally set `HAPPYX_TAG` to pin a version.
3. Deploy. To update, use **Pull and redeploy** (with "Re-pull image" on).

The images are public if the GHCR packages are public (GitHub > your profile > Packages >
happyx-api / happyx-bot > Package settings > Change visibility). If you keep them private, add
`ghcr.io` under **Registries** in Portainer with a GitHub token that has `read:packages`.
