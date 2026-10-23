# HappyX

Daily mood check-ins for Discord servers. Members answer "how are you today?" (1-5 plus an
optional message), and the server admin gets a weekly or monthly report.

| Repo | Status |
| --- | --- |
| [HappyX.Api](https://github.com/moixblau/HappyX.Api) | Go + PostgreSQL REST API, multi-server |
| [HappyX.Bot](https://github.com/moixblau/HappyX.Bot) | Go Discord bot: daily survey by DM, reports to the admin |
| [HappyX.Web](https://github.com/moixblau/HappyX.Web) | TypeScript dashboard: Discord login, mood charts and reports per server |

## Run locally

```sh
git clone --recurse-submodules https://github.com/moixblau/HappyX
cd HappyX
cp .env.example .env   # fill in a password, the Discord token and the bot's API key + its hash
docker compose up -d --build
curl localhost:8083/health
```

The dashboard is at http://localhost:8084. To log in, fill the `DISCORD_CLIENT_*` and
`SESSION_SECRET` variables in `.env` (see the comments there).

See HappyX.Bot's README for creating the Discord application (it needs the Server Members intent).

Secrets live only in `.env`, which is git-ignored. See HappyX.Api's README for how API keys work.

## Deploy with Portainer

The HappyX.Api, HappyX.Bot and HappyX.Web workflows publish multi-arch images (amd64 and arm64) to GHCR on
every merge to `main` (`latest`, `sha-<commit>`) and on `v*` tags (`1.2.3`).

1. In Portainer, go to **Stacks > Add stack > Repository**, use this repo and the compose path
   `deploy/portainer-stack.yml`. Or paste that file into the web editor.
2. Under **Environment variables**, set `POSTGRES_PASSWORD`, `API_KEY_HASHES`, `DISCORD_TOKEN` and
   `BOT_API_KEY`. For the dashboard also set `WEB_URL`, `DISCORD_CLIENT_ID`,
   `DISCORD_CLIENT_SECRET` and `SESSION_SECRET`, and add `<WEB_URL>/api/web/auth/callback` to the
   Discord application's OAuth2 redirects. Optionally set `HAPPYX_TAG` to pin a version.
3. Point your HTTPS reverse proxy at the dashboard's port (`WEB_PORT`, default 8084). Only the
   dashboard needs to be public; the API's port can stay closed.
4. Deploy. To update, use **Pull and redeploy** (with "Re-pull image" on).

The images are public if the GHCR packages are public (GitHub > your profile > Packages >
happyx-api / happyx-bot / happyx-web > Package settings > Change visibility). If you keep them private, add
`ghcr.io` under **Registries** in Portainer with a GitHub token that has `read:packages`.

### Upgrading from Postgres 17 to 18

A new major version cannot read the old data files, so the stack now uses a new volume
(`db_data_pg18`) and the data is moved with a dump. The old `db_data` volume is left untouched,
so going back is just redeploying the previous stack file.

1. Before redeploying, dump the running database:
   `docker exec <db container> pg_dump -U happyx -d happyx | gzip > happyx-pg17.sql.gz`
   (the latest file in the backups folder works too).
2. Redeploy the stack with the new `deploy/portainer-stack.yml`. Postgres 18 starts empty and the
   API creates the tables.
3. Stop the `api` and `bot` containers, then restore:
   `docker exec -i <db container> psql -U happyx -d happyx -c 'DROP SCHEMA public CASCADE; CREATE SCHEMA public;'`
   `gunzip -c happyx-pg17.sql.gz | docker exec -i <db container> psql -U happyx -d happyx`
4. Start `api` and `bot` again. Once everything looks right, delete the old `db_data` volume.

For local development, the same applies to `docker compose`: dump, `docker compose up -d`,
restore. Or start from an empty database.
