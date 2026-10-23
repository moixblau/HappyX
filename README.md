# HappyX

Daily mood check-ins for Discord servers. Members answer "how are you today?" (1-5 plus an
optional message), and the server admin gets a weekly or monthly report.

| Repo | Status |
| --- | --- |
| [HappyX.Api](https://github.com/moixblau/HappyX.Api) | Go + PostgreSQL REST API, multi-server |
| [HappyX.Bot](https://github.com/moixblau/HappyX.Bot) | Old Slack prototype; to be rewritten as a Go Discord bot |
| [HappyX.Web](https://github.com/moixblau/HappyX.Web) | Old static prototype; TypeScript web app later |

## Run locally

```sh
git clone --recurse-submodules https://github.com/moixblau/HappyX
cd HappyX
cp .env.example .env   # fill in a password and an API key hash
docker compose up -d --build
curl localhost:8083/health
```

Secrets live only in `.env`, which is git-ignored. See HappyX.Api's README for how API keys work.
