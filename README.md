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
