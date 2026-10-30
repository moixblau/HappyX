# Contributing to HappyX

Thanks for helping. HappyX is split into a few repositories; open issues and pull requests in the
one the change belongs to.

| Change | Repository |
| --- | --- |
| Routes, data, anonymity rules, reports, migrations | [HappyX.Api](https://github.com/happyx-app/HappyX.Api) |
| Discord messages, slash commands, report image, translations | [HappyX.Bot](https://github.com/happyx-app/HappyX.Bot) |
| Dashboard screens, charts, settings | [HappyX.Web](https://github.com/happyx-app/HappyX.Web) |
| Example API calls | [HappyX.Bruno](https://github.com/happyx-app/HappyX.Bruno) |
| Docs, screenshots, Compose files | this repository |

Not sure where something goes? Open an issue here.

## Ground rules

- Keep every server's data apart: the Discord server is part of every query.
- Anonymity is a promise: answers given while a server was anonymous stay anonymous.
- A change to the API's routes or request bodies comes with the matching change in HappyX.Bruno.
- Run the tests before opening a pull request: `go test ./...` in the API and the bot,
  `npm test` in the web. Each repository's README has its development notes.
- User-facing text goes through the locale files, in English first. Other languages can follow.

By contributing you agree that your work is published under the [GNU AGPL-3.0](LICENSE) (MIT in HappyX.Bruno).
