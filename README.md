<div align="center">

<img src="docs/images/logo.svg" alt="HappyX logo" width="112" height="112">

# HappyX

**How is your Discord server really doing?**

A Discord bot that asks your team how they feel every working day, anonymously,
and turns the answers into reports and charts the admin can actually read.

[![Live](https://img.shields.io/badge/live-happyx.joanbe.net-8b5cf6?style=flat-square)](https://happyx.joanbe.net)
[![Discord bot](https://img.shields.io/badge/Discord-bot-5865F2?style=flat-square&logo=discord&logoColor=white)](https://happyx.joanbe.net)
[![Go](https://img.shields.io/badge/Go-1.27-00ADD8?style=flat-square&logo=go&logoColor=white)](https://github.com/happyx-app/HappyX.Api)
[![TypeScript](https://img.shields.io/badge/TypeScript-Vite-3178C6?style=flat-square&logo=typescript&logoColor=white)](https://github.com/happyx-app/HappyX.Web)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-18-4169E1?style=flat-square&logo=postgresql&logoColor=white)](deploy/portainer-stack.yml)
[![Docker](https://img.shields.io/badge/images-GHCR-fbbf24?style=flat-square&logo=docker&logoColor=white)](#self-host-with-portainer)

[Website](https://happyx.joanbe.net) ·
[Features](#features) ·
[Screenshots](#screenshots) ·
[Architecture](#architecture) ·
[Self-host](#self-host-with-portainer) ·
[Development](#run-it-locally)

<br>

<img src="docs/images/landing.png" alt="HappyX landing page: 'How is your Discord server really doing?' next to the daily survey as it looks in Discord" width="880">

</div>

<br>

## How it works

| 1. Add the bot | 2. Everyone gets a daily question | 3. The admin gets the report |
| --- | --- | --- |
| Invite HappyX to your server. It registers the server and its members, and keeps every server's data apart, so one bot serves as many servers as you like. | On working days each member (bots aside) gets a DM: pick a face from 😞 to 🤩, add a comment if they want. Anonymous by default. | Every week or month the admin gets an image with the average mood, the daily trend and how answers spread, plus a web dashboard with charts. |

<div align="center">
<img src="docs/images/discord-survey.png" alt="The daily survey in Discord: five mood buttons and 'Don't ask me again'" width="620">
</div>

## Features

<table>
<tr>
<td width="33%" valign="top">

### 🕶️ Anonymous by default
Admins see results, not names, unless the server chooses otherwise. Answers given while
anonymous **stay anonymous** even if the server goes public later.

</td>
<td width="33%" valign="top">

### 🖼️ Image reports
A weekly or monthly summary by DM, readable at a glance, in the server's language. The DM can be
turned off, and `/happyx report` shows it on demand.

</td>
<td width="33%" valign="top">

### 📈 Web dashboard
Log in with Discord to see each server's charts and messages for any period, and **My
evolution**: your own mood across all your servers. Light and dark theme.

</td>
</tr>
<tr>
<td valign="top">

### 🙌 Kudos
`/happyx kudos @someone` thanks a teammate in public. They get a DM, and the reports count and
quote them.

</td>
<td valign="top">

### 🎲 «Who's who?»
A weekly icebreaker: everyone answers a question, then guesses on the web who wrote what. The
team proposes next week's question.

</td>
<td valign="top">

### ⚙️ Your rules
Working days, survey time and timezone, report frequency, language, and your own survey message
with variables like `{{user.name}}` or `{{user.streak}}`.

</td>
</tr>
<tr>
<td valign="top">

### 🎯 Role filters
Choose who gets the survey and who plays the game: everyone, everyone except some roles, or only
some roles.

</td>
<td valign="top">

### 🔑 Admins and permissions
One main admin receives the reports. Give other members or Discord roles **view** (reports) or
**manage** (also settings) access.

</td>
<td valign="top">

### 🔒 Privacy built in
One-click opt-out for members, GDPR export and erase of your own data, server export, and a
danger zone that deletes a server and all its data.

</td>
</tr>
<tr>
<td valign="top">

### 🏢 Many servers, no mixing
The Discord server is the tenant key in every query. The same person in two servers is two
separate histories.

</td>
<td valign="top">

### 🌍 Multilingual
The bot speaks English, Spanish, Catalan, German, French, Italian and Brazilian Portuguese; the
dashboard English and Spanish. Adding a language is one JSON file.

</td>
<td valign="top">

### 💾 Easy to run
Three small Docker images on GHCR (amd64 and arm64), one Portainer stack, and nightly Postgres
backups with rotation.

</td>
</tr>
</table>

## Screenshots

**Server dashboard.** Average mood, daily trend, answers by mood and the (anonymous) messages, for
any period.

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="docs/images/report-dark.png">
  <img src="docs/images/report.png" alt="Server dashboard: average mood, answers, daily mood chart, answers by mood and anonymous messages">
</picture>

**My evolution.** Every member sees their own answers, one line per server. Nobody else does.

<img src="docs/images/evolution.png" alt="My evolution: your own mood over the month, one line per server">

<table>
<tr>
<th width="50%">The report image the admin receives</th>
<th width="50%">«Who's who?» on the web</th>
</tr>
<tr>
<td valign="top"><img src="docs/images/report-image.png" alt="Weekly report image: average mood, answers, people, kudos, daily trend and distribution"></td>
<td valign="top"><img src="docs/images/game.png" alt="The Who's who game: guess who wrote each answer"></td>
</tr>
</table>

**Kudos in Discord.**

<img src="docs/images/discord-kudos.png" alt="A kudos card posted by HappyX in Discord" width="620">

<sub>Screenshots use sample data.</sub>

## Architecture

```mermaid
flowchart LR
    members(["👥 Members"]) <-- "DM survey,<br>slash commands" --> discord["Discord"]
    people(["🧑‍💼 Admins and members"]) -- "HTTPS" --> web
    discord <--> bot
    subgraph stack["Docker stack · Portainer"]
        bot["🤖 HappyX.Bot<br>Go"] -- "/v1 · API key" --> api["⚙️ HappyX.Api<br>Go · clean architecture"]
        web["🌐 HappyX.Web<br>TypeScript · nginx"] -- "/api/web · session" --> api
        api --> db[("🐘 PostgreSQL")]
        backup["💾 Nightly backups"] --> db
    end
```

Only the dashboard is public. The bot talks to Discord outbound, and the API stays on the stack's
internal network: nginx in HappyX.Web forwards `/api/web/*` to it, so the browser only ever talks
to one origin.

| Repository | What it is |
| --- | --- |
| **[HappyX](https://github.com/happyx-app/HappyX)** | This repo: the stack (Docker Compose and Portainer), with the others as submodules |
| [HappyX.Api](https://github.com/happyx-app/HappyX.Api) | Go REST API on PostgreSQL. Multi-server isolation, anonymity rules, reports, game, GDPR |
| [HappyX.Bot](https://github.com/happyx-app/HappyX.Bot) | Go Discord bot: daily survey by DM, slash commands, report images, kudos, game DMs |
| [HappyX.Web](https://github.com/happyx-app/HappyX.Web) | TypeScript dashboard without a framework, built with Vite and served by nginx. Charts are plain SVG |
| [HappyX.Bruno](https://github.com/happyx-app/HappyX.Bruno) | [Bruno](https://www.usebruno.com/) collection with every API call |

Each component's README goes into the details: the API's routes, anonymity rules and credentials,
the bot's commands and how to add a language, the dashboard's screens and who sees what.

## Self-host with Portainer

The HappyX.Api, HappyX.Bot and HappyX.Web workflows publish multi-arch images (amd64 and arm64) to
GHCR on every merge to `main` (`latest`, `sha-<commit>`) and on `v*` tags (`1.2.3`):
`ghcr.io/happyx-app/happyx-api`, `ghcr.io/happyx-app/happyx-bot` and `ghcr.io/happyx-app/happyx-web`.

1. Create the Discord application (see [Setting up the Discord application](https://github.com/happyx-app/HappyX.Bot#setting-up-the-discord-application)
   in HappyX.Bot). It needs the **Server Members** intent.
2. In Portainer, go to **Stacks > Add stack > Repository**, use this repo and the compose path
   `deploy/portainer-stack.yml`. Or paste that file into the web editor.
3. Under **Environment variables**, set the variables below, and add
   `<WEB_URL>/api/web/auth/callback` to the Discord application's OAuth2 redirects.
4. Point your HTTPS reverse proxy at the dashboard's port (`WEB_PORT`,
   default 8084). Only the dashboard needs to be public; the API's port can stay closed.
5. Deploy. To update, use **Pull and redeploy** (with "Re-pull image" on).

### Environment variables

| Variable | Required | What it is |
| --- | :---: | --- |
| `POSTGRES_PASSWORD` | ✅ | Database password |
| `API_KEY_HASHES` | ✅ | SHA-256 hash(es) of the API keys, comma separated |
| `DISCORD_TOKEN` | ✅ | Discord bot token |
| `BOT_API_KEY` | ✅ | The bot's API key (its hash must be in `API_KEY_HASHES`) |
| `WEB_URL` | for login | Public URL of the dashboard, e.g. `https://happyx.example.com` |
| `DISCORD_CLIENT_ID` | for login | Discord application > OAuth2 > Client ID |
| `DISCORD_CLIENT_SECRET` | for login | Discord application > OAuth2 > Client Secret |
| `SESSION_SECRET` | for login | Signs login sessions: `openssl rand -hex 32` |
| `HAPPYX_TAG` | | Image tag to deploy (default `latest`; e.g. `1.0.0` or `sha-abc1234`) |
| `WEB_PORT` | | Host port for the dashboard (default 8084) |
| `API_PORT` | | Host port for the API, bound to 127.0.0.1 only (default 8083) |
| `BACKUP_DIR` | | Host folder for backups (default: the `db_backups` volume; `chown -R 999:999` it) |

To make an API key and its hash:

```sh
KEY=$(openssl rand -hex 32); echo "key:  $KEY"; echo -n "$KEY" | sha256sum
```

Give the key to the client (`BOT_API_KEY`) and put only the hash in `API_KEY_HASHES`. See
[HappyX.Api's README](https://github.com/happyx-app/HappyX.Api#credentials) for how API keys work.

The images are public if the GHCR packages are public (the happyx-app organization > Packages >
happyx-api / happyx-bot / happyx-web > Package settings > Change visibility). If you keep them
private, add `ghcr.io` under **Registries** in Portainer with a GitHub token that has
`read:packages`.

### Backups

Every night the `backup` service dumps the database, keeping 7 daily, 4 weekly and 6 monthly
copies. To restore one (this replaces the current data):

```sh
docker exec -i <db container> psql -U happyx -d happyx -c 'DROP SCHEMA public CASCADE; CREATE SCHEMA public;'
gunzip -c <backup>.sql.gz | docker exec -i <db container> psql -U happyx -d happyx
```

<details>
<summary><b>Upgrading from Postgres 17 to 18</b></summary>

<br>

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

</details>

## Run it locally

```sh
git clone --recurse-submodules https://github.com/happyx-app/HappyX
cd HappyX
cp .env.example .env   # fill in a password, the Discord token and the bot's API key + its hash
docker compose up -d --build
curl localhost:8083/health
```

The dashboard is at http://localhost:8084. To log in, fill the `DISCORD_CLIENT_*` and
`SESSION_SECRET` variables in `.env` (see the comments there) and add
`http://localhost:8084/api/web/auth/callback` to the Discord application's OAuth2 redirects.

Secrets live only in `.env`, which is git-ignored.

Working on one piece? Each repo has its own development notes: `go test ./...` in the API and
the bot, `npm test` and `npm run dev` in the web, and `go run ./cmd/preview` in the bot renders
every message it sends as a Discord-styled page, no Discord server needed.

<br>

<div align="center">
<img src="docs/images/logo.svg" alt="" width="40" height="40">
<br>
<sub>Made with 💜 by <a href="https://github.com/moixblau">Joan</a> · <a href="https://happyx.joanbe.net">happyx.joanbe.net</a></sub>
</div>
