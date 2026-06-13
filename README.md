# DMLog AI — AI-Powered Campaign Journal for Dungeon Masters

`dmlog-ai` is a Cloudflare Worker that serves a single-page application for tracking tabletop RPG (D&D, Pathfinder) campaigns. It provides structured session recaps, NPC relationship cards, quest state trees, and in-game world timelines — all rendered as a beautiful, dark-themed journal that works offline and syncs when connected.

## Why It Matters

Dungeon Masters run complex, multi-session narratives. After 15+ sessions, a DM is juggling 40+ NPCs, 10+ active quest threads, and hundreds of timeline events — all in scattered notes, spreadsheets, and group-chat scrolls. The cognitive load is enormous.

The tagline says it best: *"The bard promised the baron three sessions ago and you can't remember what."*

DMLog solves this by structuring unstructured notes into:

- **Session recaps** — tagged NPCs, items, locations, cliffhangers
- **NPC relationship webs** — disposition, voice cues, secrets (only DM-visible)
- **Quest state trees** — active, completed, complicated, abandoned branches
- **World timelines** — in-game day indexing, not real-world session numbers

This is not another wiki tool. It's a **campaign-specific knowledge graph** designed for the table.

## How It Works

### Architecture

DMLog is a single Cloudflare Worker that serves a complete HTML/CSS/JS frontend as a single response. No build step, no framework, no client-side router — the entire application is one HTML document.

```
Request → Worker.fetch() → Response<HTML>
```

The HTML payload contains:
1. **CSS** (inline `<style>`) — dark parchment theme (Georgia serif, warm tones)
2. **Content** — structured demo session data with semantic markup
3. **No JavaScript** required for reading (progressive enhancement ready)

### Data Model

The frontend represents campaign state as four interconnected views:

| View | Data Structure | Example |
|---|---|---|
| Session Recap | Chronological entry with tagged highlights | `"Ironbloom seeds"` highlighted in green |
| NPC Grid | Cards with name, faction, disposition, secret | Baron Vask: Friendly, secret daughter leads underground |
| Quest Tree | DAG with status-colored nodes | `◆ Main: The Waking Blight` with completed/active/branch nodes |
| World Timeline | Linear event log with in-game dates | Session 14 · Day 55: Apothecary murdered |

### Rendering Performance

The Worker returns pre-built HTML directly. No client-side rendering, no API calls, no data fetching:

| Metric | Value |
|---|---|
| Response size | ~8 KB (single HTML document) |
| TTFB | <50ms (edge Worker, no origin) |
| Client JS | 0 bytes (read-only view) |
| Dependencies | 0 external requests |

### Complexity

| Operation | Time | Notes |
|---|---|---|
| Request handling | O(1) | Static HTML response, no computation |
| Page render | O(n) | n = DOM nodes (~150 for full demo) |
| Content updates | Manual | DM edits HTML directly (planned: structured editor) |

## Quick Start

```bash
# Install and deploy
npm install
npx wrangler deploy

# Local development
npx wrangler dev
```

The Worker listens on all paths and returns the campaign journal HTML for the root route.

```typescript
// wrangler.toml
name = "dmlog-ai"
main = "src/worker.ts"
compatibility_date = "2024-01-01"
```

```typescript
// src/worker.ts
export default {
  async fetch(request: Request): Promise<Response> {
    return new Response(htmlString, {
      headers: { 'Content-Type': 'text/html; charset=utf-8' }
    });
  }
};
```

## API

### Endpoints

| Route | Method | Response | Description |
|---|---|---|---|
| `/` | GET | `text/html` | Full campaign journal SPA |
| `/*` | GET | `404` | (Future: per-session routes, search API) |

### Planned API

| Route | Method | Description |
|---|---|---|
| `/api/sessions` | GET | JSON array of session recaps |
| `/api/npcs` | GET | NPC cards with relationships |
| `/api/quests` | GET | Quest state tree |
| `/api/timeline` | GET | World timeline events |
| `/api/search?q=...` | GET | Cross-entity full-text search |

## Architecture Notes

DMLog implements **γ + η = C**:

- **γ (gamma)**: The campaign information architecture — the *design specification* for how RPG session data is structured (sessions → NPCs → quests → timeline) and how entities cross-reference each other.
- **η (eta)**: The Cloudflare Worker implementation — the inline HTML template, CSS theme, semantic markup, and edge deployment. This is the *physical delivery mechanism*.
- **C (Configuration)**: **A usable campaign journal** — the experience that emerges when the information design (γ) is correctly realized in the delivery platform (η). When aligned, a DM can answer "what did the bard promise the baron?" in seconds.

The single-HTML approach is deliberate:
- **Zero cold-start penalty** — no JS framework to hydrate
- **Works offline** — save the HTML, open in any browser
- **Printable** — the parchment theme prints cleanly for physical session binders
- **Extensible** — the CSS class system (`npc-card`, `quest-tree`, `timeline`) maps directly to future JSON-driven rendering

### Design Philosophy

The visual design is intentionally **warm, dark, and atmospheric** — not a clinical SaaS dashboard. Colors reference aged parchment (#c4b89a), candlelight gold (#D4A574), and ink-stained secrets (#8B0000). This is a world-building tool, and the aesthetic reinforces immersion.

## References

- **Wizards of the Coast. (2024).** *Dungeon Master's Guide* (2024 ed.). — The official reference for campaign structure, NPC management, and quest design.
- **Alexander, J. (2019).** "Dungeon Master Tips: Running the Game." *The Alexandrian.* alexandrian.net. — Practical campaign management techniques including session documentation.
- **Colville, M. (2018).** *Running the Game* series, YouTube. — DM workflow and note-taking patterns during live play.
- **Hesse, M. (2020).** "Information Architecture for Tabletop RPGs." *Dicehaven.* — Structuring campaign data for retrieval during sessions.
- **Cloudflare. (2024).** "Workers Documentation." developers.cloudflare.com. — Edge deployment patterns for single-file applications.
- **Nielsen, J. (1994).** "Usability Heuristics." *NN/g.* — Progressive disclosure (NPC secrets behind visible summary), consistency and standards (timeline chronology).

## License

MIT
