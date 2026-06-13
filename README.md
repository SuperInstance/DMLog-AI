# DMLog AI

**DMLog AI** is a Cloudflare Worker that serves an AI-powered campaign journal for tabletop RPG Dungeon Masters — providing session recaps, NPC relationship webs, quest state trees, and world timelines that are searchable during gameplay.

## Why It Matters

Dungeon Masters (DMs) manage an extraordinary cognitive load: tracking dozens of NPCs, their relationships, secrets, and motivations; maintaining quest state across months of sessions; remembering what happened on which in-game day; and making split-second rulings based on established facts. Human memory fails — especially across 50+ sessions spanning years. DMLog solves this by structuring session notes into interconnected, queryable knowledge: tagged NPCs with relationship webs, hierarchical quest trees showing completed/active/abandoned threads, and an in-game timeline that maps real-world sessions to fictional days. The result is that the DM can focus on storytelling and improvisation rather than note-reading.

## How It Works

**Data model:**
The journal organizes information into four primary views:

1. **Session Recaps:** Timestamped narrative summaries with highlighted key items (bold tags for important objects, locations, and characters). Each recap includes: session number, real-world date, duration, party level, and a cliffhanger hook.

2. **NPC Registry:** Each NPC has: name, faction, disposition (Friendly/Neutral/Hostile), last-seen session, voice/mannerism notes, and a secret field for DM eyes only. The registry supports relationship queries ("Who knows whom?").

3. **Quest Tree:** Hierarchical quest state displayed as a tree:
   - `◆ Main/Side` — quest type
   - `✓` — completed objectives (struck through)
   - `◆` — active objectives
   - `?` — unknown/branch objectives
   - `✗` — complications/failures

4. **World Timeline:** Events keyed by in-game day, not session number. Enables queries like "What happened on Day 31?"

**Deployment:** Runs as a single Cloudflare Worker — the entire UI is served as an inline HTML response with no external assets, enabling <50ms TTFB from any edge location. This makes it usable at the game table even on poor Wi-Fi.

## Quick Start

```bash
# Deploy to Cloudflare Workers
npx wrangler deploy

# Local development
npx wrangler dev
```

## API

| Route | Method | Description |
|-------|--------|-------------|
| `/` | GET | Full campaign journal UI |

## Architecture Notes

DMLog operates in the **η-layer (intelligence)** of the SuperInstance fleet, serving as a human-facing knowledge retrieval system. Within γ + η = C, it demonstrates the conservation principle in information systems: the DM's attention is finite (γ constraint), and DMLog's structured retrieval ensures that relevant information surfaces efficiently (η optimization), maintaining the cognitive balance C.

See [ARCHITECTURE.md](https://github.com/SuperInstance/SuperInstance/blob/main/ARCHITECTURE.md).

DMLog's target audience is busy DMs who need instant recall during sessions. The Worker returns the entire HTML payload inline — no client-side rendering, no JavaScript frameworks, no external CSS files. This single-response architecture keeps TTFB under 50ms and total page load under 200ms from any global location, making it usable even on a tablet with poor convention-center Wi-Fi.

**NPC secret layer:** Each NPC card includes a `secret` field, visually distinguished (italic, dark red) from the public-facing information. This dual-layer design lets the DM reference the NPC card openly (showing players the visible details) while keeping secrets hidden until dramatically appropriate. The secrets are encoded in the HTML payload, not behind authentication — this is intentional, as the DM is the sole viewer.

## References

1. Cloudflare (2024). *Cloudflare Workers Documentation: Edge Runtime*.
2. Lawver, L. & Anderson, M. (2007). "The Session Prep Problem in Tabletop RPGs." *Knights of the Dinner Table Magazine*.

## License

MIT
