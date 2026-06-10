# DMLog

> Campaign journal for tabletop RPGs.

**[dmlog.ai](https://dmlog.ai)**

Session notes that actually make sense when you read them back. NPC relationships, quest states, world timeline — all connected, all searchable. Because your players remember everything and you should too.

## What It Does

- **Session recaps** — Dictate or type notes during the game; DMLog structures them into searchable recaps
- **NPC relationship webs** — Track who knows whom, who owes whom, who's secretly related to whom
- **Quest state trees** — Main quests, side quests, abandoned threads at a glance
- **World timeline** — In-game days, not real-world sessions
- **Quick search** — "What did the bard promise the baron?" → instant answer with session reference
- **Offline support** — No Wi-Fi at the table required

## Tech Stack

- Cloudflare Workers (edge deployment)
- Single-file HTML response
- Custom domain via Cloudflare

## Deployment

```bash
npx wrangler deploy
```

## Part of [SuperInstance](https://superinstance.ai)
